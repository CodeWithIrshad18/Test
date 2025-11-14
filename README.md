<div id="videoContainer" class="scanning">
    <div class="face-brackets">
        <span class="corner tl"></span>
        <span class="corner tr"></span>
        <span class="corner bl"></span>
        <span class="corner br"></span>
    </div>

    <video id="video" autoplay muted playsinline></video>
    <img id="capturedImage" style="display:none;" />
</div>

<div id="statusText">Align your face inside the frame</div>
/* -------- FACE ID 4 CORNER BRACKETS -------- */

.face-brackets {
    position: absolute;
    width: 92%;
    height: 92%;
    top: 4%;
    left: 4%;
    pointer-events: none;
}

.corner {
    position: absolute;
    width: 40px;
    height: 40px;
    border: 4px solid #1e90ff;
    border-radius: 10px;
    opacity: 1;
    animation: cornerGlow 2s ease-in-out infinite;
}

/* Top-left */
.corner.tl {
    top: 0;
    left: 0;
    border-right: none;
    border-bottom: none;
}

/* Top-right */
.corner.tr {
    top: 0;
    right: 0;
    border-left: none;
    border-bottom: none;
}

/* Bottom-left */
.corner.bl {
    bottom: 0;
    left: 0;
    border-right: none;
    border-top: none;
}

/* Bottom-right */
.corner.br {
    bottom: 0;
    right: 0;
    border-left: none;
    border-top: none;
}

/* Corner Glow Animation */
@keyframes cornerGlow {
    0% { opacity: 0.5; transform: scale(1); }
    50% { opacity: 1; transform: scale(1.1); }
    100% { opacity: 0.5; transform: scale(1); }
}

/* SUCCESS = Green */
#videoContainer.success .corner {
    border-color: #00c853;
    box-shadow: 0 0 18px rgba(0, 200, 83, 0.5);
}

/* ERROR = Red */
#videoContainer.error .corner {
    border-color: #ff1744;
    box-shadow: 0 0 18px rgba(255, 23, 68, 0.5);
}

/* Default scanning state = Blue */
#videoContainer.scanning .corner {
    border-color: #1e90ff;
}

videoContainer.classList.remove("success", "error");
videoContainer.classList.add("scanning");

videoContainer.classList.remove("scanning", "error");
videoContainer.classList.add("success");

videoContainer.classList.remove("scanning", "success");
videoContainer.classList.add("error");



css:
<style>

    video {
        transform: scaleX(-1);
    }


    #videoContainer {
        width: 250px;
        height: 250px;
        border-radius: 50%;
        overflow: hidden;
        border: 6px solid transparent;
        transition: border-color 0.3s ease;
        display: flex;
        justify-content: center;
        align-items: center;
        background: #f5f5f5;
        margin: auto;
    }


    #video, #capturedImage {
        width: 270px !important;
        height: 270px !important;
        object-fit: cover;
        border-radius: 50%;
      
    }

    #statusText {
        font-weight: bold;
        margin-top: 15px;
        font-size: 15px;
        color: #444;
    }
</style>

html:

      <div id="videoContainer">
    <div id="faceOverlay" class="face-overlay"></div>
    <video id="video" autoplay muted playsinline></video>
    <img id="capturedImage" style="display:none;" />
</div>


js:
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

                 Swal.close();


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
