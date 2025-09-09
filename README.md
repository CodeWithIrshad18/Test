<script>
    let locationCheckInterval = null;

    async function OnOff() {
        const punchIn = document.getElementById('PunchIn');
        const punchOut = document.getElementById('PunchOut');

        // Disable buttons first
        if (punchIn) { punchIn.disabled = true; punchIn.classList.add("disabled"); }
        if (punchOut) { punchOut.disabled = true; punchOut.classList.add("disabled"); }

        try {
            const position = await getCurrentPosition({ enableHighAccuracy: true, timeout: 10000 });
            const lat = roundTo(position.coords.latitude, 6);
            const lon = roundTo(position.coords.longitude, 6);

            const locations = @Html.Raw(Json.Serialize(ViewBag.PolyData));

            let isInsideRadius = false;
            let minDistance = Number.MAX_VALUE;

            locations.forEach((loc) => {
                const allowedRange = parseFloat(loc.range || loc.Range);
                const distance = calculateDistance(
                    lat,
                    lon,
                    loc.latitude || loc.Latitude,
                    loc.longitude || loc.Longitude
                );
                if (distance <= allowedRange) {
                    isInsideRadius = true;
                } else {
                    minDistance = Math.min(minDistance, distance);
                }
            });

            if (isInsideRadius) {
                // ✅ Inside allowed range
                if (punchIn) { punchIn.disabled = false; punchIn.classList.remove("disabled"); }
                if (punchOut) { punchOut.disabled = false; punchOut.classList.remove("disabled"); }

                // 👉 Only now start face recognition
                initFaceRecognitionLogic();

            } else {
                // ❌ Outside range
                Swal.fire({
                    icon: "error",
                    title: "Out of Range",
                    text: `You are ${Math.round(minDistance)} meters away from the allowed location!`,
                    showConfirmButton: true,
                    allowOutsideClick: false
                });
            }

        } catch (error) {
            let msg = "Please check your location permission or enable location services.";
            if (error.code === 1) msg = "Permission denied. Please allow location access.";
            if (error.code === 2) msg = "Location unavailable. Please try again.";
            if (error.code === 3) msg = "Location request timed out.";

            Swal.fire({
                icon: "error",
                title: "Error Fetching Location!",
                text: msg,
                confirmButtonText: "OK"
            });
        }
    }

    function getCurrentPosition(options) {
        return new Promise((resolve, reject) => {
            navigator.geolocation.getCurrentPosition(resolve, reject, options);
        });
    }

    function calculateDistance(lat1, lon1, lat2, lon2) {
        const R = 6371000; // meters
        const toRad = angle => (angle * Math.PI) / 180;
        const dLat = toRad(lat2 - lat1);
        const dLon = toRad(lon2 - lon1);
        const a = Math.sin(dLat / 2) ** 2 +
            Math.cos(toRad(lat1)) * Math.cos(toRad(lat2)) *
            Math.sin(dLon / 2) ** 2;
        return R * (2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a)));
    }

    function roundTo(num, places) {
        return +(Math.round(num + "e" + places) + "e-" + places);
    }

    window.onload = () => {
        OnOff(); // First check location
        if (locationCheckInterval) clearInterval(locationCheckInterval);
        locationCheckInterval = setInterval(OnOff, 60000); // re-check location every 60s
    };

    // 👉 Wrap your full face recognition script in this function
    function initFaceRecognitionLogic() {
        // paste your full face recognition <script> logic here
        // (the one you already shared earlier)
    }
</script>





for (int i = 0; i < PageRecordDataSet.Tables["Table1"].Rows.Count; i++)
{
    DataRow newRow = PageRecordDataSet.Tables["App_Bonus_Generation_Details"].NewRow();

    foreach (DataColumn col in PageRecordDataSet.Tables["App_Bonus_Generation_Details"].Columns)
    {
        if (PageRecordDataSet.Tables["Table1"].Columns.Contains(col.ColumnName)) // ✅ check
        {
            var val = PageRecordDataSet.Tables["Table1"].Rows[i][col.ColumnName];
            newRow[col.ColumnName] = (val == DBNull.Value || string.IsNullOrWhiteSpace(val.ToString())) ? 0 : val;
        }
        else
        {
            newRow[col.ColumnName] = 0; // agar Table1 me column missing hai
        }
    }

    PageRecordDataSet.Tables["App_Bonus_Generation_Details"].Rows.Add(newRow);
}



this is my face recognition logic 
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
        const entryType = document.getElementById("Entry").value;

        if (punchInButton) punchInButton.style.display = "none";
        if (punchOutButton) punchOutButton.style.display = "none";

        Swal.fire({
            title: 'Please wait...',
            text: 'Preparing face recognition.',
            allowOutsideClick: false,
            didOpen: () => Swal.showLoading()
        });

        startVideo();

        Promise.all([
            faceapi.nets.tinyFaceDetector.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceLandmark68TinyNet.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceRecognitionNet.loadFromUri('/TSUISLARS/faceApi')
        ]).then(async () => {
            const dummy = document.createElement("canvas");
            dummy.width = 160; dummy.height = 160;
            await faceapi.detectSingleFace(dummy, new faceapi.TinyFaceDetectorOptions());
            Swal.close();
            initFaceRecognition();
        });

        function startVideo() {
            navigator.mediaDevices.getUserMedia({
                video: {
                    facingMode: "user",
                    width: { ideal: 640 },
                    height: { ideal: 480 }
                }
            })
            .then(stream => {
                video.srcObject = stream;
            })
            .catch(console.error);
        }

        function stopVideo() {
            const stream = video.srcObject;
            if (stream) {
                stream.getTracks().forEach(track => track.stop());
            }
            video.srcObject = null;
        }

    
        function verifyDescriptor(descriptor, faceMatcher, matchMode, baseDescriptor, capturedDescriptor) {
            const match = faceMatcher.findBestMatch(descriptor);

            if (match.label !== userId || match.distance >= 0.35) {
                return { success: false, reason: "No proper match" };
            }

            if (matchMode === "both") {
                const distToBase = faceapi.euclideanDistance(descriptor, baseDescriptor);
                const distToCaptured = faceapi.euclideanDistance(descriptor, capturedDescriptor);

                if (distToBase < 0.35 && distToCaptured < 0.35) {
                    return { success: true };
                } else {
                    return { success: false, reason: "Face not aligned (tilted/poor image)" };
                }
            }

            return { success: true }; // baseOnly mode
        }

        async function initFaceRecognition() {
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
                statusText.textContent = "❌ No reference image found. Please upload your image.";
                return;
            }

            let faceMatcher = null;
            let matchMode = "";

            if (baseDescriptor && capturedDescriptor) {
                faceMatcher = new faceapi.FaceMatcher(
                    [new faceapi.LabeledFaceDescriptors(userId, [baseDescriptor, capturedDescriptor])],
                    getThreshold()
                );
                matchMode = "both";
            } else if (baseDescriptor) {
                faceMatcher = new faceapi.FaceMatcher(
                    [new faceapi.LabeledFaceDescriptors(userId, [baseDescriptor])],
                    getThreshold()
                );
                matchMode = "baseOnly";
            } else {
                statusText.textContent = "⚠️ Only captured image found. Please upload your image.";
                return;
            }

            let lastFailureTime = 0;
            function logFailure() {
                const now = Date.now();
                if (now - lastFailureTime < 10000) return;
                lastFailureTime = now;

                fetch("/TSUISLARS/Geo/LogFaceMatchFailure", {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ Type: entryType })
                }).catch(err => console.error("Error logging failure:", err));
            }

            let matchFound = false;
            let detectionInterval = null;

            if (detectionInterval) clearInterval(detectionInterval);
            detectionInterval = setInterval(async () => {
                if (matchFound) return;

                const detections = await faceapi
                    .detectAllFaces(video, new faceapi.TinyFaceDetectorOptions({ inputSize: 160, scoreThreshold: 0.5 }))
                    .withFaceLandmarks(true)
                    .withFaceDescriptors();

                if (detections.length === 0) {
                    statusText.textContent = "No face detected";
                    videoContainer.style.borderColor = "gray";
                    return;
                }

                if (detections.length > 1) {
                    statusText.textContent = "❌ Multiple faces detected. Please ensure only one face is visible.";
                    videoContainer.style.borderColor = "red";
                    return;
                }

                const detection = detections[0];
                const result = verifyDescriptor(detection.descriptor, faceMatcher, matchMode, baseDescriptor, capturedDescriptor);

                if (result.success) {
                    onMatchSuccess(detection.descriptor);
                } else {
                    statusText.textContent = "❌ " + result.reason;
                    videoContainer.style.borderColor = "red";
                    logFailure();
                }
            }, 300);

            function onMatchSuccess(descriptor) {
                statusText.textContent = `${userName}, Face matched ✅`;
                matchFound = true;
                window.lastVerifiedDescriptor = descriptor;
                videoContainer.style.borderColor = "green";
                setTimeout(() => showSuccessAndCapture(), 1000);
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
                        .detectSingleFace(img, new faceapi.TinyFaceDetectorOptions({ inputSize: 160 }))
                        .withFaceLandmarks(true)
                        .withFaceDescriptor();
                    return detection?.descriptor || null;
                } catch {
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
                }, 2000);
            }

        
               window.captureImageAndSubmit = async function (entryType) {
        if (!window.capturedDataURL) {
            alert("❌ No captured face image found.");
            statusText.textContent = "Please try again.";
            return;
        }

       
        const detection = await faceapi
            .detectSingleFace(capturedImage, new faceapi.TinyFaceDetectorOptions({ inputSize: 160 }))
            .withFaceLandmarks(true)
            .withFaceDescriptor();

        if (!detection) {
            statusText.textContent = "❌ No face detected in captured image. Please retry.";
            videoContainer.style.borderColor = "red";
            return resetToRetry();
        }

     
        const result = verifyDescriptor(detection.descriptor, faceMatcher, matchMode, baseDescriptor, capturedDescriptor);

        if (!result.success) {
            statusText.textContent = "❌ " + result.reason;
            videoContainer.style.borderColor = "red";
            return resetToRetry();
        }

     
        statusText.textContent = "✅ Verified! Submitting...";
        EntryTypeInput.value = entryType;

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
                    .then(() => {
                        stopVideo();
                        location.reload();
                    });
            } else {
                Swal.fire("Face Verified, But Error!", "Server rejected attendance.", "error")
                    .then(() => {
                        stopVideo();
                        location.reload();
                    });
            }
        })
        .catch(() => {
            Swal.fire("Error!", "Submission failed.", "error");
        });
    };

            function getThreshold() {
                const ua = navigator.userAgent.toLowerCase();
                return ua.includes("android") ? 0.42 : 0.35;
            }
        }
    });
</script>

and this is my location logic 
<script>
    let locationCheckInterval = null;

    async function OnOff() {
        const punchIn = document.getElementById('PunchIn');
        const punchOut = document.getElementById('PunchOut');

       
        if (punchIn) {
            punchIn.disabled = true;
            punchIn.classList.add("disabled");
        }
        if (punchOut) {
            punchOut.disabled = true;
            punchOut.classList.add("disabled");
        }

        try {
            const position = await getCurrentPosition({ enableHighAccuracy: true, timeout: 10000 });
            const lat = roundTo(position.coords.latitude, 6);
            const lon = roundTo(position.coords.longitude, 6);

            const locations = @Html.Raw(Json.Serialize(ViewBag.PolyData));

            let isInsideRadius = false;
            let minDistance = Number.MAX_VALUE;

            locations.forEach((loc) => {
                const allowedRange = parseFloat(loc.range || loc.Range);
                const distance = calculateDistance(
                    lat,
                    lon,
                    loc.latitude || loc.Latitude,
                    loc.longitude || loc.Longitude
                );

                if (distance <= allowedRange) {
                    isInsideRadius = true;
                } else {
                    minDistance = Math.min(minDistance, distance);
                }
            });

            if (isInsideRadius) {
                if (punchIn) {
                    punchIn.disabled = false;
                    punchIn.classList.remove("disabled");
                }
                if (punchOut) {
                    punchOut.disabled = false;
                    punchOut.classList.remove("disabled");
                }
            } else {
                if (punchIn) {
                    punchIn.disabled = true;
                    punchIn.classList.add("disabled");
                }
                if (punchOut) {
                    punchOut.disabled = true;
                    punchOut.classList.add("disabled");
                }

                Swal.fire({
                    icon: "error",
                    title: "Out of Range",
                    text: `You are ${Math.round(minDistance)} meters away from the allowed location!`,
                    showConfirmButton:true,
                    allowOutsideClick:false
                });
            }

        } catch (error) {
            let msg = "Please check your location permission or enable location services.";
            if (error.code === 1) msg = "Permission denied. Please allow location access.";
            if (error.code === 2) msg = "Location unavailable. Please try again.";
            if (error.code === 3) msg = "Location request timed out.";

            Swal.fire({
                icon: "error",
                title: "Error Fetching Location!",
                text: msg,
                confirmButtonText: "OK"
            });

            if (punchIn) {
                punchIn.disabled = true;
                punchIn.classList.add("disabled");
            }
            if (punchOut) {
                punchOut.disabled = true;
                punchOut.classList.add("disabled");
            }
        }
    }

    function getCurrentPosition(options) {
        return new Promise((resolve, reject) => {
            navigator.geolocation.getCurrentPosition(resolve, reject, options);
        });
    }

    function calculateDistance(lat1, lon1, lat2, lon2) {
        const R = 6371000; // meters
        const toRad = angle => (angle * Math.PI) / 180;
        const dLat = toRad(lat2 - lat1);
        const dLon = toRad(lon2 - lon1);

        const a = Math.sin(dLat / 2) ** 2 +
            Math.cos(toRad(lat1)) * Math.cos(toRad(lat2)) *
            Math.sin(dLon / 2) ** 2;

        return R * (2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a)));
    }

    function roundTo(num, places) {
        return +(Math.round(num + "e" + places) + "e-" + places);
    }

    window.onload = () => {
        OnOff(); // Initial check
        if (locationCheckInterval) clearInterval(locationCheckInterval);
        // Re-check every 60 seconds
        locationCheckInterval = setInterval(OnOff, 60000);
    };
</script>

there is problem on message conflict , I want to show fetch the location first if it is out f range then it shows face recognition logic, I want proper message for users to understand properly 
