<style>
    video {
        transform: scaleX(-1);
        -webkit-transform: scaleX(-1);
        -moz-transform: scaleX(-1);
    }
</style>

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

    <div class="mt-3 form-group">
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

        <div class="issue-box text-center mt-3">
            <p>
                <i class="fa-solid fa-circle-info me-2"></i>
                Having trouble?<br>
                <a href="/TSUISLARS/Geo/GeoFencing" class="issue-link">Click here for previous version</a>
            </p>
        </div>

    </div>
</form>

<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.7.1/jquery.min.js"></script>

<script>
    const userId = '@ViewBag.UserId';
    const userName = '@ViewBag.UserName';
</script>

<script>

    async function startFaceRecognition() {

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

        /* 🔥 1) START CAMERA IMMEDIATELY (parallel to model loading) */
        const startCameraPromise = navigator.mediaDevices.getUserMedia({
            video: { facingMode: { ideal: "user" } }
        }).then(stream => {
            video.srcObject = stream;
        }).catch(console.error);

        /* 🔥 2) LOAD MODELS IN BACKGROUND (parallel) */
        const modelsPromise = Promise.all([
            faceapi.nets.tinyFaceDetector.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceLandmark68TinyNet.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceRecognitionNet.loadFromUri('/TSUISLARS/faceApi')
        ]);

        /* WAIT FOR BOTH */
        await Promise.all([startCameraPromise, modelsPromise]);

        /* 🔥 3) Camera warm-up after video starts */
        await new Promise(r => setTimeout(r, 400));

        Swal.close();

        initFaceRecognition();


        /* --------------------------
           STOP CAMERA FUNCTION
        -------------------------- */
        function stopVideo() {
            const stream = video.srcObject;
            if (stream) {
                stream.getTracks().forEach(track => track.stop());
            }
            video.srcObject = null;
        }


        /* --------------------------
              FACE VERIFICATION
        -------------------------- */
        function verifyDescriptor(descriptor, faceMatcher, mode, base, captured) {
            const match = faceMatcher.findBestMatch(descriptor);

            if (match.label !== userId || match.distance >= 0.45)
                return { success: false, reason: "Face not match with reference image." };

            if (mode === "both") {
                const distBase = faceapi.euclideanDistance(descriptor, base);
                const distCaptured = faceapi.euclideanDistance(descriptor, captured);

                if (distBase < 0.45 && distCaptured < 0.45)
                    return { success: true };
                else
                    return { success: false, reason: "Face not aligned (tilted/poor image)" };
            }

            return { success: true };
        }


        /* --------------------------
              INITIALIZE FACE-API
        -------------------------- */
        async function initFaceRecognition() {

            const safeUserName = userName.replace(/\s+/g, "%20");
            const t = Date.now();

            const baseURL = `/TSUISLARS/Images/${userId}-${safeUserName}.jpg?t=${t}`;
            const capturedURL = `/TSUISLARS/Images/${userId}-Captured.jpg?t=${t}`;

            let baseDescriptor = null, capturedDescriptor = null;

            try {
                baseDescriptor = await loadDescriptor(baseURL);
                capturedDescriptor = await loadDescriptor(capturedURL);
            } catch { }

            if (!baseDescriptor && !capturedDescriptor) {
                statusText.textContent = "❌ No reference image found.";
                return;
            }

            let faceMatcher;
            let mode;

            if (baseDescriptor && capturedDescriptor) {
                faceMatcher = new faceapi.FaceMatcher(
                    [new faceapi.LabeledFaceDescriptors(userId, [baseDescriptor, capturedDescriptor])],
                    getThreshold()
                );
                mode = "both";
            }
            else if (baseDescriptor) {
                faceMatcher = new faceapi.FaceMatcher(
                    [new faceapi.LabeledFaceDescriptors(userId, [baseDescriptor])],
                    getThreshold()
                );
                mode = "baseOnly";
            }
            else {
                statusText.textContent = "⚠️ Only captured image found. Upload your image.";
                return;
            }

            /* --------------------------
               FACE DETECTION LOOP
            -------------------------- */
            let matchFound = false;
            let fail = 0, success = 0;

            setInterval(async () => {
                if (matchFound) return;

                const detections = await faceapi
                    .detectAllFaces(video, new faceapi.TinyFaceDetectorOptions({ inputSize: 160, scoreThreshold: 0.5 }))
                    .withFaceLandmarks(true)
                    .withFaceDescriptors();

                if (detections.length === 0) {
                    statusText.textContent = "No face detected";
                    videoContainer.style.borderColor = "gray";
                    fail = success = 0;
                    return;
                }

                if (detections.length > 1) {
                    statusText.textContent = "❌ Multiple faces detected.";
                    videoContainer.style.borderColor = "red";
                    fail = success = 0;
                    return;
                }

                const d = detections[0];
                const result = verifyDescriptor(d.descriptor, faceMatcher, mode, baseDescriptor, capturedDescriptor);

                if (result.success) {
                    success++;
                    fail = 0;
                    if (success >= 2)
                        onMatch(d.descriptor);
                } else {
                    fail++;
                    success = 0;
                    if (fail >= 3) {
                        statusText.textContent = "❌ " + result.reason;
                        videoContainer.style.borderColor = "red";
                    }
                }

            }, 300);


            /* --------------------------
                 MATCH SUCCESS
            -------------------------- */
            function onMatch(descriptor) {
                matchFound = true;
                statusText.textContent = `${userName}, Face matched ✅`;
                videoContainer.style.borderColor = "green";
                window.lastVerifiedDescriptor = descriptor;
                setTimeout(capture, 300);
            }


            /* --------------------------
                 CAPTURE IMAGE
            -------------------------- */
            function capture() {

                const cap = document.createElement("canvas");
                cap.width = video.videoWidth;
                cap.height = video.videoHeight;

                const ctx = cap.getContext("2d");
                ctx.translate(cap.width, 0);
                ctx.scale(-1, 1);
                ctx.drawImage(video, 0, 0, cap.width, cap.height);

                const data = cap.toDataURL("image/jpeg");

                capturedImage.src = data;
                capturedImage.style.display = "block";
                video.style.display = "none";

                if (punchInButton) punchInButton.style.display = "inline-block";
                if (punchOutButton) punchOutButton.style.display = "inline-block";

                window.capturedDataURL = data;
            }


            /* --------------------------
                LOAD DESCRIPTOR
            -------------------------- */
            async function loadDescriptor(url) {
                try {
                    const img = await faceapi.fetchImage(url);
                    const d = await faceapi
                        .detectSingleFace(img, new faceapi.TinyFaceDetectorOptions({ inputSize: 160 }))
                        .withFaceLandmarks(true)
                        .withFaceDescriptor();

                    return d?.descriptor ?? null;
                } catch { return null; }
            }


            /* --------------------------
                SUBMIT ATTENDANCE
            -------------------------- */
            window.captureImageAndSubmit = async function (entryType) {

                if (!window.capturedDataURL) {
                    alert("❌ No captured image.");
                    return;
                }

                const detection = await faceapi
                    .detectSingleFace(capturedImage, new faceapi.TinyFaceDetectorOptions({ inputSize: 160 }))
                    .withFaceLandmarks(true)
                    .withFaceDescriptor();

                if (!detection) {
                    statusText.textContent = "❌ No face in captured image.";
                    videoContainer.style.borderColor = "red";
                    return;
                }

                const result = verifyDescriptor(detection.descriptor, faceMatcher, mode, baseDescriptor, capturedDescriptor);

                if (!result.success) {
                    statusText.textContent = "❌ " + result.reason;
                    videoContainer.style.borderColor = "red";
                    return;
                }

                statusText.textContent = "✅ Verified! Submitting...";
                EntryTypeInput.value = entryType;

                Swal.fire({
                    title: "Please wait…",
                    allowOutsideClick: false,
                    showConfirmButton: false,
                    didOpen: () => Swal.showLoading()
                });

                fetch("/TSUISLARS/Face/AttendanceData", {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ Type: entryType, ImageData: window.capturedDataURL })
                })
                .then(r => r.json())
                .then(d => {
                    const now = new Date().toLocaleString();
                    if (d.success) {
                        Swal.fire("Thank you!", `Attendance Recorded\n${now}`, "success")
                            .then(() => {
                                stopVideo();
                                location.reload();
                            });
                    } else {
                        Swal.fire("Face OK but Error!", "Server rejected attendance.", "error")
                            .then(() => {
                                stopVideo();
                                location.reload();
                            });
                    }
                })
                .catch(() => Swal.fire("Error", "Submission failed.", "error"));
            };

            function getThreshold() {
                return navigator.userAgent.toLowerCase().includes("android") ? 0.5 : 0.45;
            }
        }
    }
</script>







full code 
<style>
    video {
        transform: scaleX(-1);
        -webkit-transform: scaleX(-1);
        -moz-transform: scaleX(-1);

    }
</style>

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

    <div class="mt-3 form-group">
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

         <div class="issue-box text-center mt-3">
    <p>
        <i class="fa-solid fa-circle-info me-2"></i>
        Having trouble?<br>
        <a href="/TSUISLARS/Geo/GeoFencing" class="issue-link">Click here for previous version</a>
    </p>
</div>

    </div>
</form>


<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.7.1/jquery.min.js"></script>

<script>
    const userId = '@ViewBag.UserId';
    const userName = '@ViewBag.UserName';
</script>

<script>

    async function startFaceRecognition(){
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
                    facingMode: "user" }})
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

            if (match.label !== userId || match.distance >= 0.45) {
                return { success: false, reason: "Face not match with reference image." };
            }

            if (matchMode === "both") {
                const distToBase = faceapi.euclideanDistance(descriptor, baseDescriptor);
                const distToCaptured = faceapi.euclideanDistance(descriptor, capturedDescriptor);

                if (distToBase < 0.45 && distToCaptured < 0.45) {
                    return { success: true };
                } else {
                    return { success: false, reason: "Face not aligned (tilted/poor image)" };
                }
            }

            return { success: true }; 
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

                 startVideo();


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

                fetch("/TSUISLARS/Face/LogFaceMatchFailure", {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ Type: entryType })
                }).catch(err => console.error("Error logging failure:", err));
            }

            let matchFound = false;
            let detectionInterval = null;

            if (detectionInterval) clearInterval(detectionInterval);
            let failCount = 0;
let successCount = 0;

detectionInterval = setInterval(async () => {
    if (matchFound) return;

    const detections = await faceapi
        .detectAllFaces(video, new faceapi.TinyFaceDetectorOptions({ inputSize: 160, scoreThreshold: 0.5 }))
        .withFaceLandmarks(true)
        .withFaceDescriptors();

    if (detections.length === 0) {
        statusText.textContent = "No face detected";
        videoContainer.style.borderColor = "gray";
        failCount = 0; successCount = 0;
        return;
    }

    if (detections.length > 1) {
        statusText.textContent = "❌ Multiple faces detected. Please ensure only one face is visible.";
        videoContainer.style.borderColor = "red";
        failCount = 0; successCount = 0;
        return;
    }

    const detection = detections[0];
    const result = verifyDescriptor(
        detection.descriptor, 
        faceMatcher, 
        matchMode, 
        baseDescriptor, 
        capturedDescriptor
    );

    if (result.success) {
        successCount++;
        failCount = 0;
        if (successCount >= 2) {  
            onMatchSuccess(detection.descriptor);
        }
    } else {
        failCount++;
        successCount = 0;
        if (failCount >= 3) {    
            statusText.textContent = "❌ " + result.reason;
            videoContainer.style.borderColor = "red";
            logFailure();
        }
    }
}, 300);

            function onMatchSuccess(descriptor) {
                statusText.textContent = `${userName}, Face matched ✅`;
                matchFound = true;
                window.lastVerifiedDescriptor = descriptor;
                videoContainer.style.borderColor = "green";
                setTimeout(() => showSuccessAndCapture(), 500);
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

        fetch("/TSUISLARS/Face/AttendanceData", {
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
                return ua.includes("android") ? 0.5 : 0.45;
            }
        }
        }
</script>
