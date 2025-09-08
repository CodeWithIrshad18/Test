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

        // ⚡ Load models asynchronously in background
        Promise.all([
            faceapi.nets.tinyFaceDetector.loadFromUri('/AS/faceApi'),
            faceapi.nets.faceLandmark68TinyNet.loadFromUri('/AS/faceApi'),
            faceapi.nets.faceRecognitionNet.loadFromUri('/AS/faceApi')
        ]).then(async () => {
            // Warm-up (run once so first detection is fast)
            const dummy = document.createElement("canvas");
            dummy.width = 160; dummy.height = 160;
            await faceapi.detectSingleFace(dummy, new faceapi.TinyFaceDetectorOptions());

            Swal.close();
            initFaceRecognition();
                startVideo();
        });

        // 👉 all your recognition logic moved here after models are loaded
        async function initFaceRecognition() {
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

            

           
            let lastFailureTime = 0;
            function logFailure() {
                const now = Date.now();
                if (now - lastFailureTime < 10000) return; // cooldown
                lastFailureTime = now;

                fetch("/AS/Geo/LogFaceMatchFailure", {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ Type: entryType })
                }).catch(err => console.error("Error logging failure:", err));
            }

            let matchFound = false;

            // ⚡ Optimized detection every 300ms
            setInterval(async () => {
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
            }, 300);

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
                        .detectSingleFace(img, new faceapi.TinyFaceDetectorOptions({ inputSize: 160 }))
                        .withFaceLandmarks(true)
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
                        .detectAllFaces(img, new faceapi.TinyFaceDetectorOptions({ inputSize: 160 }))
                        .withFaceLandmarks(true)
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
                                    Swal.fire("Face Verified, But Error!", "Server rejected attendance.", "error")
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
        }
    });
</script>

worker service 
const CACHE_NAME = "faceapi-cache-v1";
const MODEL_PATH = "/AS/faceApi/"; // adjust if your path is different

// List the models you want to cache permanently
const FILES_TO_CACHE = [
    MODEL_PATH + "tiny_face_detector_model-weights_manifest.json",
    MODEL_PATH + "tiny_face_detector_model-shard1",
    MODEL_PATH + "face_landmark_68_tiny_model-weights_manifest.json",
    MODEL_PATH + "face_landmark_68_tiny_model-shard1",
    MODEL_PATH + "face_recognition_model-weights_manifest.json",
    MODEL_PATH + "face_recognition_model-shard1"
];

// Install event → cache all model files
self.addEventListener("install", (event) => {
    event.waitUntil(
        caches.open(CACHE_NAME).then((cache) => {
            return cache.addAll(FILES_TO_CACHE);
        })
    );
    self.skipWaiting();
});

// Activate event → cleanup old caches if version changes
self.addEventListener("activate", (event) => {
    event.waitUntil(
        caches.keys().then((keyList) => {
            return Promise.all(
                keyList.map((key) => {
                    if (key !== CACHE_NAME) {
                        return caches.delete(key);
                    }
                })
            );
        })
    );
    self.clients.claim();
});

// Fetch event → serve models from cache
self.addEventListener("fetch", (event) => {
    if (FILES_TO_CACHE.some((file) => event.request.url.includes(file))) {
        event.respondWith(
            caches.match(event.request).then((response) => {
                return response || fetch(event.request);
            })
        );
    }
});

why i am getting error of these 2 files in console 

face-api.min.js:1  GET https://servicesdev.tsuisl.co.in/AS/faceApi/face_recognition_model-shard2 404 (Not Found)

face-api.min.js:1 Uncaught (in promise) Error: Based on the provided shape, [3,3,256,256], the tensor should have 589824 values but has 145408
