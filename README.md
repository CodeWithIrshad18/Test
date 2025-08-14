<script>
    window.addEventListener("DOMContentLoaded", async () => {
        const video = document.getElementById("video");
        const canvas = document.getElementById("canvas");
        const capturedImage = document.getElementById("capturedImage");
        const EntryTypeInput = document.getElementById("EntryType");
        const statusText = document.getElementById("statusText");
        const videoContainer = document.getElementById("videoContainer");
        const punchInButton = document.getElementById("PunchIn");
        const punchOutButton = document.getElementById("PunchOut");

        if (punchInButton) punchInButton.style.display = "none";
        if (punchOutButton) punchOutButton.style.display = "none";

        await Promise.all([
            faceapi.nets.tinyFaceDetector.loadFromUri('/AS/faceApi'),
            faceapi.nets.faceLandmark68Net.loadFromUri('/AS/faceApi'),
            faceapi.nets.faceRecognitionNet.loadFromUri('/AS/faceApi')
        ]);

        const safeUserName = userName.replace(/\s+/g, "%20");
        const timestamp = Date.now();

        const baseImageUrl = `/AS/Images/${userId}-${safeUserName}.jpg?t=${timestamp}`;
        const capturedImageUrl = `/AS/Images/${userId}-Captured.jpg?t=${timestamp}`;

        let baseDescriptor = null;
        let capturedDescriptor = null;

        try {
            baseDescriptor = await loadDescriptor(baseImageUrl);
            capturedDescriptor = await loadDescriptor(capturedImageUrl);
        } catch (err) {
            console.warn("Error loading descriptors:", err);
        }

        if (!baseDescriptor && !capturedDescriptor) {
            statusText.textContent = "❌ No reference image(s) found. Please upload your image.";
            return;
        }

        let faceMatcher = null;
        let matchMode = "";

        if (baseDescriptor && capturedDescriptor) {
            faceMatcher = new faceapi.FaceMatcher(
                [new faceapi.LabeledFaceDescriptors(userId, [baseDescriptor, capturedDescriptor])],
                0.35
            );
            matchMode = "both";
        } else if (baseDescriptor) {
            faceMatcher = new faceapi.FaceMatcher(
                [new faceapi.LabeledFaceDescriptors(userId, [baseDescriptor])],
                0.35
            );
            matchMode = "baseOnly";
        } else {
            statusText.textContent = "⚠️ Only captured image found. Please upload your image.";
            return;
        }

        startVideo();

        function startVideo() {
            navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } })
                .then(stream => {
                    video.srcObject = stream;
                    video.onloadeddata = () => requestAnimationFrame(detectAndMatchFace);
                })
                .catch(console.error);
        }

        let matchFound = false;

        async function detectAndMatchFace() {
            if (matchFound) return;

            const detections = await faceapi
                .detectAllFaces(video, new faceapi.TinyFaceDetectorOptions({ inputSize: 320 }))
                .withFaceLandmarks()
                .withFaceDescriptors();

            if (detections.length === 0) {
                statusText.textContent = "No face detected";
                videoContainer.style.borderColor = "gray";
                return requestAnimationFrame(detectAndMatchFace);
            }

            if (detections.length > 1) {
                statusText.textContent = "❌ Multiple faces detected. Please ensure only one face is visible.";
                videoContainer.style.borderColor = "red";
                return requestAnimationFrame(detectAndMatchFace);
            }

            const detection = detections[0];
            const match = faceMatcher.findBestMatch(detection.descriptor);

            if (match.label === userId && match.distance < 0.35) {
    if (matchMode === "both") {
        const distToBase = faceapi.euclideanDistance(detection.descriptor, baseDescriptor);
        const distToCaptured = faceapi.euclideanDistance(detection.descriptor, capturedDescriptor);

        if (distToBase < 0.35 && distToCaptured < 0.35) {
            onMatchSuccess();
        } else {
            statusText.textContent = "❌ Face does not match with uploaded images.";
            videoContainer.style.borderColor = "red";
        }
    } else {
        onMatchSuccess();
    }
} else {
    statusText.textContent = "❌ Face does not match with reference images.";
    videoContainer.style.borderColor = "red";
}

            requestAnimationFrame(detectAndMatchFace);
        }

        function onMatchSuccess() {
            statusText.textContent = `${userName}, Face matched ✅`;
            matchFound = true;
            videoContainer.style.borderColor = "green";
            setTimeout(() => {
                showSuccessAndCapture();
            }, 1000);
        }

        function showSuccessAndCapture() {
            const captureCanvas = document.createElement("canvas");
            captureCanvas.width = video.videoWidth;
            captureCanvas.height = video.videoHeight;

            const ctx = captureCanvas.getContext("2d");
            ctx.translate(captureCanvas.width, 0);
            ctx.scale(-1, 1);
            ctx.drawImage(video, 0, 0, captureCanvas.width, captureCanvas.height);

            const imageCaptured = captureCanvas.toDataURL("image/jpeg");
            capturedImage.src = imageCaptured;
            capturedImage.style.display = "block";
            video.style.display = "none";

            if (punchInButton) punchInButton.style.display = "inline-block";
            if (punchOutButton) punchOutButton.style.display = "inline-block";

            window.capturedDataURL = imageCaptured;
        }

        async function loadDescriptor(imageUrl) {
            try {
                const img = await faceapi.fetchImage(imageUrl);
                const detection = await faceapi
                    .detectSingleFace(img, new faceapi.TinyFaceDetectorOptions())
                    .withFaceLandmarks()
                    .withFaceDescriptor();
                return detection?.descriptor || null;
            } catch (err) {
                console.warn(`Error loading descriptor from ${imageUrl}:`, err);
                return null;
            }
        }

        function resetToRetry() {
            setTimeout(() => {
                statusText.textContent = "Please align your face properly.";
                if (punchInButton) punchInButton.style.display = "none";
                if (punchOutButton) punchOutButton.style.display = "none";
                capturedImage.style.display = "none";
                video.style.display = "block";
                matchFound = false;
                requestAnimationFrame(detectAndMatchFace);
            }, 2000);
        }

        window.captureImageAndSubmit = async function (entryType) {
            if (!window.capturedDataURL) {
                alert("❌ No image captured.");
                statusText.textContent = "Please try again — no image captured.";
                return;
            }

            statusText.textContent = "🔍 Verifying captured image before submission...";

            try {
                const img = await faceapi.fetchImage(window.capturedDataURL);
                const detections = await faceapi
                    .detectAllFaces(img, new faceapi.TinyFaceDetectorOptions({ inputSize: 320 }))
                    .withFaceLandmarks()
                    .withFaceDescriptors();

                if (detections.length === 0) {
                    statusText.textContent = "❌ No face found in captured image.";
                    videoContainer.style.borderColor = "gray";
                    return resetToRetry();
                }

                if (detections.length > 1) {
                    statusText.textContent = "❌ Multiple faces detected in captured image.";
                    videoContainer.style.borderColor = "red";
                    return resetToRetry();
                }

                const detection = detections[0];
                const match = faceMatcher.findBestMatch(detection.descriptor);

                if (match.label === userId && match.distance < 0.35) {
                    if (matchMode === "both") {
    const distToBase = faceapi.euclideanDistance(detection.descriptor, baseDescriptor);
    const distToCaptured = faceapi.euclideanDistance(detection.descriptor, capturedDescriptor);

    if (distToBase >= 0.35 && distToCaptured >= 0.35) {
        statusText.textContent = "❌ Captured face does not match reference image.";
        videoContainer.style.borderColor = "red";

        onMatchFailure(entryType);
        return resetToRetry();
    }
}

                    
                    statusText.textContent = "✅ Verified! Submitting...";
                    EntryTypeInput.value = entryType;

                    Swal.fire({
                        title: "Please wait...",
                        allowOutsideClick: false,
                        showConfirmButton: false,
                        didOpen: () => Swal.showLoading()
                    });

                    fetch("/AS/Geo/AttendanceData", {
                        method: "POST",
                        headers: { "Content-Type": "application/json" },
                        body: JSON.stringify({ Type: entryType, ImageData: window.capturedDataURL })
                    })
                        .then(res => res.json())
                        .then(data => {
                            const now = new Date().toLocaleString();
                            if (data.success) {
                                statusText.textContent = "";
                                Swal.fire("Thank you!", `Attendance Recorded.\nDate & Time: ${now}`, "success")
                                    .then(() => location.reload());
                            } else {
                                Swal.fire("Face Verified, But Error!","Server rejected attendance.", "error")
                                    .then(() => location.reload());
                            }
                        })
                        .catch(() => {
                            Swal.fire("Error!", "Submission failed.", "error");
                        });

                } else {
                    statusText.textContent = "❌ Final face check failed. Please try again.";
                    videoContainer.style.borderColor = "red";
                    onMatchFailure(entryType);
                    return resetToRetry();
                }

            } catch (err) {
                console.error("Error during final verification:", err);
                statusText.textContent = "❌ Error during final verification. Please try again.";
            }
        };
    });

    function onMatchFailure(entryType) {
    statusText.textContent = "Face not matched ❌";
    videoContainer.style.borderColor = "red";

    fetch("/AS/Geo/LogFaceMatchFailure", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ Type: entryType })
    });
}
</script>


and this ismy controller 
[HttpPost]
public async Task<IActionResult> AttendanceData([FromBody] AttendanceRequest model)
{
    try
    {
        var userId = HttpContext.Request.Cookies["Session"];
        var userName = HttpContext.Request.Cookies["UserName"];

        if (string.IsNullOrEmpty(userId))
            return Json(new { success = false, message = "User session not found!" });

        string Pno = userId;
        string currentDate = DateTime.Now.ToString("yyyy/MM/dd");
        string currentTime = DateTime.Now.ToString("HH:mm");
        DateTime today = DateTime.Today;

        
        string? pyrlArea = null;

        

        using (var connection = new SqlConnection(configuration.GetConnectionString("RFID")))
        {
            await connection.OpenAsync();
            string query = "SELECT ema_pyrl_area FROM SAPHRDB.dbo.T_EMPL_ALL WHERE ema_perno = @Pno";
            pyrlArea = await connection.QueryFirstOrDefaultAsync<string>(query, new { Pno });
        }

        

        var record = await context.AppFaceVerificationDetails
            .FirstOrDefaultAsync(x => x.Pno == Pno && x.DateAndTime.Value.Date == today);

        if (record == null)
        {
            record = new AppFaceVerificationDetail
            {
                Pno = Pno,
                DateAndTime = DateTime.Now,
                PunchInFailedCount= 0,
                PunchOutFailedCount= 0,
                PunchInSuccess = (model.Type == "Punch In"),
                PunchOutSuccess = (model.Type == "Punch Out")
            };
            context.AppFaceVerificationDetails.Add(record);
        }
        else
        {
            if (model.Type == "Punch In")
                record.PunchInSuccess = true;
            else if (model.Type == "Punch Out")
                record.PunchOutSuccess = true;
        }

        if (model.Type == "Punch In")
        {
            string newCapturedPath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/Images/", $"{Pno}-Captured.jpg");
            SaveBase64ImageToFile(model.ImageData, newCapturedPath);

            StoreData(currentDate, currentTime, null, Pno);
            if (pyrlArea == "JS" || pyrlArea == "ZZ")
                StoreDataNOPR(currentDate, currentTime, null, Pno);
        }
        else
        {
            StoreData(currentDate, null, currentTime, Pno);
            if (pyrlArea == "JS" || pyrlArea == "ZZ")
                StoreDataNOPR(currentDate, null, currentTime, Pno);
        }

        await context.SaveChangesAsync();
        return Json(new { success = true, message = "Attendance recorded successfully." });
    }
    catch (Exception ex)
    {
        return Json(new { success = false, message = ex.Message });
    }
}


[HttpPost]
public IActionResult LogFaceMatchFailure([FromBody] AttendanceRequest model)
{
    try
    {
        var userId = HttpContext.Request.Cookies["Session"];
        if (string.IsNullOrEmpty(userId))
            return Json(new { success = false, message = "User session not found!" });

        DateTime today = DateTime.Today;
        var record = context.AppFaceVerificationDetails
            .FirstOrDefault(x => x.Pno == userId && x.DateAndTime.Value.Date == today);

        string type = (model.Type ?? "").Trim().ToLower();

        if (record == null)
        {
            
            record = new AppFaceVerificationDetail
            {
                Pno = userId,
                DateAndTime = DateTime.Now,
                PunchInFailedCount = (type == "punch in") ? 1 : 0,
                PunchOutFailedCount = (type == "punch out") ? 1 : 0,
                PunchInSuccess = false,
                PunchOutSuccess = false
            };
            context.AppFaceVerificationDetails.Add(record);
        }
        else
        {
            
            if (type == "punch in")
                record.PunchInFailedCount += 1;
            else if (type == "punch out")
                record.PunchOutFailedCount += 1;
        }

        context.SaveChanges();
        return Json(new { success = true, message = "Face match failure logged.", type });
    }
    catch (Exception ex)
    {
        return Json(new { success = false, message = ex.Message });
    }
}

i want to store on punchInFailedCount and PunchOutFailed count when live video of recognition is happening not on any button click
