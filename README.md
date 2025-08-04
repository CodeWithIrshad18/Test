[HttpPost]
public async Task<IActionResult> UpdateAttendance([FromBody] AttendanceRequest model)
{
    try
    {
        // Get user info from cookie
        string userInfoJson = Request.Cookies["UserInfo"];
        if (string.IsNullOrEmpty(userInfoJson))
        {
            return Json(new { success = false, message = "User info not found." });
        }

        var userInfo = JsonSerializer.Deserialize<UserInfo>(userInfoJson);
        if (userInfo == null || string.IsNullOrEmpty(userInfo.UserId))
        {
            return Json(new { success = false, message = "Invalid user info." });
        }

        string Pno = userInfo.UserId;
        DateTime today = DateTime.Today;

        // Save captured image
        if (!string.IsNullOrEmpty(model.ImageData))
        {
            string base64 = model.ImageData;
            var base64Data = Regex.Match(base64, @"data:image/(?<type>.+?),(?<data>.+)").Groups["data"].Value;
            byte[] imageBytes = Convert.FromBase64String(base64Data);

            string folderPath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot", "Images");
            if (!Directory.Exists(folderPath))
            {
                Directory.CreateDirectory(folderPath);
            }

            string fileName = $"{Pno}-Captured.jpg";
            string filePath = Path.Combine(folderPath, fileName);
            await System.IO.File.WriteAllBytesAsync(filePath, imageBytes);
        }

        // Check if attendance record exists
        var record = await context.AppFaceVerificationDetails
            .FirstOrDefaultAsync(x => x.Pno == Pno && x.DateAndTime.Value.Date == today);

        if (record != null)
        {
            if (model.Type == "Punch In")
            {
                record.PunchInSuccess = true;
            }
            else
            {
                record.PunchOutSuccess = true;
            }

            await context.SaveChangesAsync();
        }

        // Always return success to avoid frontend error alert
        return Json(new { success = true });
    }
    catch (Exception ex)
    {
        return Json(new { success = false, message = "Server error: " + ex.Message });
    }
}





var record = await 
context.AppFaceVerificationDetails
    .FirstOrDefaultAsync(x => x.Pno == Pno && x.DateAndTime.Value.Date == today);

if (record == null)
{
    record = new AppFaceVerificationDetail
    {
        Pno = Pno,
        DateAndTime = DateTime.Now,
        PunchInSuccess = false,
        PunchOutSuccess = false
    };
    context.AppFaceVerificationDetails.Add(record);
}





this is my controller code 

 [HttpPost]
 public async Task<IActionResult> AttendanceData([FromBody] AttendanceRequest model)
 {
     try
     {
         var UserId = HttpContext.Request.Cookies["Session"];
         var UserName = HttpContext.Request.Cookies["UserName"];
         if (string.IsNullOrEmpty(UserId))
             return Json(new { success = false, message = "User session not found!" });


         string Pno = UserId;
         string Name = UserName;

         bool isFaceMatched = true;


         string currentDate = DateTime.Now.ToString("yyyy/MM/dd");
         string currentTime = DateTime.Now.ToString("HH:mm");

         DateTime today = DateTime.Today;

         var record = await context.AppFaceVerificationDetails
            .FirstOrDefaultAsync(x => x.Pno == Pno && x.DateAndTime.Value.Date == today);

         if (isFaceMatched)
         {
             if (model.Type == "Punch In")
             {
                 string newCapturedPath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/Images/", $"{Pno}-Captured.jpg");
                 SaveBase64ImageToFile(model.ImageData, newCapturedPath);

                 StoreData(currentDate, currentTime, null, Pno);
                 record.PunchInSuccess = true;
             }
             else
             {
                 StoreData(currentDate, null, currentTime, Pno);
                 record.PunchOutSuccess = true;
             }

            await context.SaveChangesAsync();
             
         }

         return Json(new { success = true, message = "Attendance recorded successfully." });

         //return Json(new { success = false, message = "Face verification failed." });

     }
     catch (Exception ex)
     {
         return Json(new { success = false, message = ex.Message });
     }
 }

and this is my js 

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
            faceapi.nets.tinyFaceDetector.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceLandmark68Net.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceRecognitionNet.loadFromUri('/TSUISLARS/faceApi')
        ]);

        const safeUserName = userName.replace(/\s+/g, "%20");
        const timestamp = Date.now();

        const baseImageUrl = `/TSUISLARS/Images/${userId}-${safeUserName}.jpg?t=${timestamp}`;
        const capturedImageUrl = `/TSUISLARS/Images/${userId}-Captured.jpg?t=${timestamp}`;

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
        } else if (capturedDescriptor) {
           
            statusText.textContent = ⚠️ Only captured image found. Cannot proceed with matching.";
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

        let lastMismatchLoggedTime = 0;
        let matchFound = false;

        async function detectAndMatchFace() {
            if (matchFound) return;

            const detection = await faceapi.detectSingleFace(video, new faceapi.TinyFaceDetectorOptions({ inputSize: 320 }))
                .withFaceLandmarks()
                .withFaceDescriptor();

            if (!detection) {
                statusText.textContent = "No face detected";
                videoContainer.style.borderColor = "gray";
                return requestAnimationFrame(detectAndMatchFace);
            }

            const match = faceMatcher.findBestMatch(detection.descriptor);

            if (match.label === userId && match.distance < 0.35) {
                if (matchMode === "both") {
                    
                    const distToBase = faceapi.euclideanDistance(detection.descriptor, baseDescriptor);
                    const distToCaptured = faceapi.euclideanDistance(detection.descriptor, capturedDescriptor);
                    if (distToBase < 0.35 && distToCaptured < 0.35) {
                        onMatchSuccess();
                    } else {
                        onMatchFailure();
                    }
                } else if (matchMode === "baseOnly") {
                    onMatchSuccess();
                }
            } else {
                onMatchFailure();
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

        function onMatchFailure() {
            statusText.textContent = "Face not matched ❌";
            videoContainer.style.borderColor = "red";

            const now = Date.now();
            const cooldownMs = 5000;

            if (now - lastMismatchLoggedTime >= cooldownMs) {
                lastMismatchLoggedTime = now;

                const entryType = document.getElementById("Entry")?.value || "";
                fetch("/TSUISLARS/Geo/LogFaceMatchFailure", {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ Type: entryType })
                });
            }
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

        window.captureImageAndSubmit = async function (entryType) {
            if (!window.capturedDataURL) {
                alert("No capture image available");
                statusText.textContent = "Please try again — no image captured.";
                capturedImage.style.display = "none";
                video.style.display = "block";
                if (punchInButton) punchInButton.style.display = "none";
                if (punchOutButton) punchOutButton.style.display = "none";
                requestAnimationFrame(detectAndMatchFace);
                return;
            }

            EntryTypeInput.value = entryType;
            capturedImage.style.display = "block";
            video.style.display = "none";

            Swal.fire({
                title: "Please wait...",
                allowOutsideClick: false,
                showConfirmButton: false,
                didOpen: () => Swal.showLoading()
            });

            fetch("/TSUISLARS/Geo/AttendanceData", {
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
                        Swal.fire("Face Recognized, But Error!", "Server rejected attendance.", "error")
                        .then(() => location.reload());
                    }
                })
                .catch(() => {
                    Swal.fire("Error!", "Submission failed.", "error");
                });
        };
    });
</script>

I am getting this error showing on Sweetalert why? everything is proper to and stores the data but getting this error I have this reference image
