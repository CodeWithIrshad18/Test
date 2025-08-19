<script>
    const userId = '@ViewBag.UserId';
    const userName = '@ViewBag.UserName';

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

        // 🚀 Start camera immediately
        startVideo();

        // 🔔 Show "loading models" instead of "Fetching location"
        Swal.fire({
            title: 'Please wait...',
            text: 'Preparing face recognition models.',
            allowOutsideClick: false,
            didOpen: () => Swal.showLoading()
        });

        // 📦 Load face-api models in background
        await Promise.all([
            faceapi.nets.tinyFaceDetector.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceLandmark68Net.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceRecognitionNet.loadFromUri('/TSUISLARS/faceApi')
        ]);

        // ✅ Close loading once models are ready
        Swal.close();

        // 🔄 Now load user descriptors
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
        } else {
            statusText.textContent = "⚠️ Only captured image found. Please upload your image.";
            return;
        }

        // ✅ Begin face detection loop
        requestAnimationFrame(() => detectAndMatchFace(faceMatcher, matchMode));

        // ✅ After models ready, also run location check
        OnOff();

        // ---- Functions ----
        function startVideo() {
            navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } })
                .then(stream => {
                    video.srcObject = stream;
                })
                .catch(console.error);
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

        // 👇 keep your existing detectAndMatchFace, onMatchSuccess, showSuccessAndCapture, 
        // captureImageAndSubmit, logFailure, resetToRetry here (unchanged).
    });
</script>



this is my main logic of js, it takes too much time , when page is starting of video is taking so much time , please start it as soon as possible

<form asp-action="AttendanceData" id="form" asp-controller="Geo" method="post">
    <div class="text-center camera">
        <div id="videoContainer" style="display: inline-block;width: 195px; border: 4px solid transparent; border-radius: 8px; transition: border-color 0.3s ease;">
            <video id="video" width="185" height="240" autoplay muted playsinline></video>
            <img id="capturedImage" style="display:none; width: 186px; height: 240px; border-radius: 8px;" />
        </div>
        <canvas id="canvas" style="display:none;"></canvas>
        <p id="statusText" style="font-weight: bold; margin-top: 10px; color: #444;"></p>
    </div>

    

    <input type="hidden" name="Type" id="EntryType" />
    <input type="hidden" id="Entry" value="@((ViewBag.InOut == "I") ? "Punch In" : "Punch Out")" />

    <div class="mt-5 form-group">
        <div class="col d-flex justify-content-center mb-4">
            @if (ViewBag.InOut == "I")
            {
                <button type="button" class="Btn" id="PunchIn" onclick="captureImageAndSubmit('Punch In')">Punch In</button>
            }
        </div>
        <div class="col d-flex justify-content-center">
            @if (ViewBag.InOut == "O")
            {
                <button type="button" class="Btn2" id="PunchOut" onclick="captureImageAndSubmit('Punch Out')">Punch Out</button>
            }
        </div>
    </div>
</form>

<script>
    const userId = '@ViewBag.UserId';
    const userName = '@ViewBag.UserName';
</script>

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

        let lastFailureTime = 0;

function logFailure() {
    const now = Date.now();
    if (now - lastFailureTime < 10000) return; // 3 sec cooldown
    lastFailureTime = now;

    fetch("/TSUISLARS/Geo/LogFaceMatchFailure", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ Type: entryType })
    }).catch(err => console.error("Error logging failure:", err));
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
                logFailure(); 
            }
        } else {
            onMatchSuccess();
        }
    } else {
        
        statusText.textContent = "❌ Face does not match with reference images.";
        videoContainer.style.borderColor = "red";
        logFailure(); 
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
                   
                    return resetToRetry();
                }

            } catch (err) {
                console.error("Error during final verification:", err);
                statusText.textContent = "❌ Error during final verification. Please try again.";
            }
        };
    });
</script>

<script>
    function OnOff() {
        setTimeout(() => {
            var punchIn = document.getElementById('PunchIn');
            var punchOut = document.getElementById('PunchOut');


            if (punchIn) {
                punchIn.disabled = true;
                punchIn.classList.add("disabled");
            }
            if (punchOut) {
                punchOut.disabled = true;
                punchOut.classList.add("disabled");
            }

            Swal.fire({
                title: 'Please wait...',
                text: 'Fetching your current location.',
                allowOutsideClick: false,
                didOpen: () => {
                    Swal.showLoading();
                }
            });

            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(
                    function (position) {
                        Swal.close();

                        const lat = roundTo(position.coords.latitude, 6);
                        const lon = roundTo(position.coords.longitude, 6);
                        

                        const locations = @Html.Raw(Json.Serialize(ViewBag.PolyData));


                        let isInsideRadius = false;
                        let minDistance = Number.MAX_VALUE;

                        locations.forEach((location) => {
                            const allowedRange = parseFloat(location.range || location.Range);
                            const distance = calculateDistance(lat, lon, location.latitude || location.Latitude, location.longitude || location.Longitude);
                            
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
                            Swal.fire({
                                icon: "error",
                                title: "Out of Range",
                                text: `You are ${Math.round(minDistance)} meters away from the allowed location!`
                            });
                        }
                    },
                    function (error) {
                        Swal.close();
                        Swal.fire({
                            title: "Error Fetching Location!",
                            text: "please check your location permission or enable location",
                            icon: "error",
                            confirmButtonText: "OK"
                        });
                    },
                    {
                        enableHighAccuracy: true,
                        timeout: 10000,
                        maximumAge: 0
                    }
                );
            } else {
                Swal.close();
                alert("Geolocation is not supported by this browser");
            }
        }, 500);
    }


    window.onload = OnOff;

    function calculateDistance(lat1, lon1, lat2, lon2) {
        const R = 6371000;
        const toRad = angle => (angle * Math.PI) / 180;
        let dLat = toRad(lat2 - lat1);
        let dLon = toRad(lon2 - lon1);
        let a = Math.sin(dLat / 2) * Math.sin(dLat / 2) +
            Math.cos(toRad(lat1)) * Math.cos(toRad(lat2)) *
            Math.sin(dLon / 2) * Math.sin(dLon / 2);
        let c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
        return R * c;
    }

    function roundTo(num, places) {
        return +(Math.round(num + "e" + places) + "e-" + places);
    }

    window.onload = OnOff;
</script>
