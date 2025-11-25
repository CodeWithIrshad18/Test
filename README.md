async function startFaceRecognition() {
    const video = document.getElementById("video");
    const canvas = document.getElementById("canvas");
    const capturedImage = document.getElementById("capturedImage");
    const EntryTypeInput = document.getElementById("EntryType");
    const statusText = document.getElementById("statusText");
    const videoContainer = document.getElementById("videoContainer");
    const entryType = document.getElementById("Entry").value;

    Swal.fire({
        title: 'Preparing...',
        text: 'Loading face recognition resources.',
        allowOutsideClick: false,
        didOpen: () => Swal.showLoading()
    });

    // ⏳ Load models only once (IndexedDB cached)
    await loadModelsOnce();

    // ⏳ Refresh descriptors only if user uploaded new image
    await prepareDescriptors();

    Swal.close();
    initFaceRecognition();
    
    function startVideo() {
        navigator.mediaDevices.getUserMedia({
            video: {
                facingMode: "user",
                width: { ideal: 640 },
                height: { ideal: 480 }
            }
        })
        .then(stream => video.srcObject = stream)
        .catch(console.error);
    }

    function stopVideo() {
        const stream = video.srcObject;
        if (stream) stream.getTracks().forEach(track => track.stop());
        video.srcObject = null;
    }

    async function initFaceRecognition() {
        const safeUserName = userName.replace(/\s+/g, "%20");
        const timestamp = Date.now();

        const baseImageUrl = `/TSUISLARS/Images/${userId}-${safeUserName}.jpg?t=${timestamp}`;
        const capturedImageUrl = `/TSUISLARS/Images/${userId}-Captured.jpg?t=${timestamp}`;

        // Load descriptors from user photos ONLY if not already cached
        let baseD = baseDescriptor;
        let capturedD = capturedDescriptor;

        startVideo();

        if (!baseD && !capturedD) {
            statusText.textContent = "❌ No base image found. Please upload your image.";
            return;
        }

        let matchMode = "";
        if (baseD && capturedD) {
            faceMatcher = new faceapi.FaceMatcher(
                [new faceapi.LabeledFaceDescriptors(userId, [baseD, capturedD])],
                getThreshold()
            );
            matchMode = "both";
        } else if (baseD) {
            faceMatcher = new faceapi.FaceMatcher(
                [new faceapi.LabeledFaceDescriptors(userId, [baseD])],
                getThreshold()
            );
            matchMode = "baseOnly";
        }

        let matchFound = false;
        let failCount = 0;
        let successCount = 0;

        setInterval(async () => {
            if (matchFound) return;

            const detections = await faceapi
                .detectAllFaces(video, new faceapi.TinyFaceDetectorOptions({ inputSize: 160, scoreThreshold: 0.5 }))
                .withFaceLandmarks(true)
                .withFaceDescriptors();

            if (detections.length === 0) {
                statusText.textContent = "No face detected";
                videoContainer.className = "scanning";
                failCount = successCount = 0;
                return;
            }

            if (detections.length > 1) {
                statusText.textContent = "❌ Multiple faces detected.";
                videoContainer.className = "error";
                failCount = successCount = 0;
                return;
            }

            const detection = detections[0];
            const result = verifyDescriptor(detection.descriptor, faceMatcher, matchMode, baseD, capturedD);

            if (result.success) {
                successCount++;
                failCount = 0;
                if (successCount >= 2) onMatchSuccess(detection.descriptor);
            } else {
                failCount++;
                successCount = 0;
                if (failCount >= 3) {
                    statusText.textContent = "❌ " + result.reason;
                    videoContainer.className = "error";
                }
            }
        }, 300);

        async function onMatchSuccess() {
            matchFound = true;

            const captureCanvas = document.createElement("canvas");
            captureCanvas.width = video.videoWidth;
            captureCanvas.height = video.videoHeight;

            const ctx = captureCanvas.getContext("2d");
            ctx.translate(captureCanvas.width, 0);
            ctx.scale(-1, 1); 
            ctx.drawImage(video, 0, 0);

            const imgDataURL = captureCanvas.toDataURL("image/jpeg");

            capturedImage.src = imgDataURL;
            capturedImage.style.display = "block";
            video.style.display = "none";

            statusText.textContent = `${userName}, Face matched ✅`;
            videoContainer.className = "success";

            await new Promise(res => setTimeout(res, 1500));

            Swal.fire({
                title: "Submitting...",
                allowOutsideClick: false,
                showConfirmButton: false,
                didOpen: () => Swal.showLoading()
            });

            fetch("/TSUISLARS/Face/AttendanceData", {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ Type: entryType, ImageData: imgDataURL })
            })
            .then(r => r.json())
            .then(data => {
                stopVideo();
                const now = new Date().toLocaleString();
                Swal.fire(
                    data.success ? "Thank you!" : "Error!",
                    data.success ? `Attendance Recorded.<br>${now}` : data.message,
                    data.success ? "success" : "error"
                ).then(() => location.reload());
            });
        }
    }

    function verifyDescriptor(descriptor, faceMatcher, matchMode, baseDescriptor, capturedDescriptor) {
        const match = faceMatcher.findBestMatch(descriptor);
        if (match.label !== userId || match.distance >= 0.45) return { success: false, reason: "Face not match with reference image." };

        if (matchMode === "both") {
            const d1 = faceapi.euclideanDistance(descriptor, baseDescriptor);
            const d2 = faceapi.euclideanDistance(descriptor, capturedDescriptor);
            if (d1 < 0.45 && d2 < 0.45) return { success: true };
            return { success: false, reason: "Face not match with reference image." };
        }
        return { success: true };
    }

    function getThreshold() {
        return navigator.userAgent.toLowerCase().includes("android") ? 0.5 : 0.45;
    }
}



<script>
let modelsLoaded = false;
let descriptorReady = false;

let baseDescriptor = null;
let capturedDescriptor = null;
let faceMatcher = null;

const modelCacheFlag = "faceModelsCached";
const descriptorCacheKey = `descriptor_${userId}`;


// ---------------------------------------------------------------
// 1️⃣ LOAD MODELS ONLY ONCE (IndexedDB CACHED)
// ---------------------------------------------------------------
async function loadModelsOnce() {
    if (modelsLoaded) return;

    const exists = localStorage.getItem(modelCacheFlag);

    if (!exists) {
        console.log("⬇ First load → loading models from server...");

        await Promise.all([
            faceapi.nets.tinyFaceDetector.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceLandmark68TinyNet.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceRecognitionNet.loadFromUri('/TSUISLARS/faceApi')
        ]);

        console.log("📦 Saving models to IndexedDB...");
        await faceapi.nets.tinyFaceDetector.save("indexedDB://tinyFaceDetector");
        await faceapi.nets.faceLandmark68TinyNet.save("indexedDB://landmarkNet");
        await faceapi.nets.faceRecognitionNet.save("indexedDB://recognitionNet");

        localStorage.setItem(modelCacheFlag, "true");
    } else {
        console.log("⚡ Loading models from IndexedDB (fast)...");
        try {
            await Promise.all([
                faceapi.nets.tinyFaceDetector.load("indexedDB://tinyFaceDetector"),
                faceapi.nets.faceLandmark68TinyNet.load("indexedDB://landmarkNet"),
                faceapi.nets.faceRecognitionNet.load("indexedDB://recognitionNet")
            ]);
        } catch {
            console.warn("⚠ IndexedDB corrupted → reloading from server...");
            localStorage.removeItem(modelCacheFlag);
            return await loadModelsOnce();
        }
    }

    modelsLoaded = true;
    console.log("✔ Models Ready");
}


// ---------------------------------------------------------------
// 2️⃣ DESCRIPTOR CACHE — RELOAD ONLY IF USER UPLOADED NEW IMAGE
// ---------------------------------------------------------------
async function prepareDescriptors() {
    const timestamp = await fetch(`/TSUISLARS/Face/GetImageVersion?userId=${userId}`)
        .then(r => r.json());

    const cached = JSON.parse(localStorage.getItem(descriptorCacheKey));

    if (!cached || cached.version !== timestamp) {
        console.log("🆕 New face detected → refreshing descriptors");

        const encodedName = encodeURIComponent(userName);

        baseDescriptor = await loadDescriptor(`/TSUISLARS/Images/${userId}-${encodedName}.jpg?t=${timestamp}`);
        capturedDescriptor = await loadDescriptor(`/TSUISLARS/Images/${userId}-Captured.jpg?t=${timestamp}`);

        localStorage.setItem(descriptorCacheKey, JSON.stringify({
            version: timestamp,
            base: baseDescriptor,
            captured: capturedDescriptor
        }));
    } else {
        console.log("♻ Using cached descriptors");
        baseDescriptor = cached.base;
        capturedDescriptor = cached.captured;
    }

    descriptorReady = true;
}


// Helper: convert image to face descriptor
async function loadDescriptor(url) {
    try {
        const img = await faceapi.fetchImage(url);
        const detection = await faceapi
            .detectSingleFace(img, new faceapi.TinyFaceDetectorOptions({ inputSize: 160 }))
            .withFaceLandmarks()
            .withFaceDescriptor();
        return detection?.descriptor || null;
    } catch {
        return null;
    }
}


// ---------------------------------------------------------------
// 3️⃣ MAIN FACE RECOGNITION PROCESS
// ---------------------------------------------------------------
async function startFaceRecognition() {
    const video = document.getElementById("video");
    const canvas = document.getElementById("canvas");
    const capturedImage = document.getElementById("capturedImage");
    const statusText = document.getElementById("statusText");
    const videoContainer = document.getElementById("videoContainer");
    const entryType = document.getElementById("Entry").value;

    Swal.fire({
        title: 'Preparing...',
        text: 'Loading face recognition...',
        allowOutsideClick: false,
        didOpen: () => Swal.showLoading()
    });

    // 🔥 ONE-TIME MODEL LOAD (IndexedDB cached)
    await loadModelsOnce();

    // 🔥 Descriptor reload only if user changed image
    await prepareDescriptors();

    Swal.close();

    initFaceRecognition();


    // ---------------- CAMERA & MATCHING ----------------
    function startVideo() {
        navigator.mediaDevices.getUserMedia({
            video: { facingMode: "user", width: { ideal: 640 }, height: { ideal: 480 } }
        }).then(stream => video.srcObject = stream);
    }

    function stopVideo() {
        const stream = video.srcObject;
        if (stream) stream.getTracks().forEach(t => t.stop());
        video.srcObject = null;
    }


    async function initFaceRecognition() {
        startVideo();

        if (!baseDescriptor && !capturedDescriptor) {
            statusText.textContent = "❌ No face registered. Please upload image.";
            return;
        }

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
        }

        let matchFound = false, fail = 0, success = 0;

        setInterval(async () => {
            if (matchFound) return;

            const detections = await faceapi
                .detectAllFaces(video, new faceapi.TinyFaceDetectorOptions({ inputSize: 160 }))
                .withFaceLandmarks()
                .withFaceDescriptors();

            if (detections.length !== 1) {
                statusText.textContent = detections.length === 0 ? "No face detected" : "❌ Multiple faces";
                videoContainer.className = detections.length === 0 ? "scanning" : "error";
                fail = success = 0;
                return;
            }

            const detection = detections[0];
            const verified = verifyDescriptor(detection.descriptor);

            if (verified) {
                success++;
                fail = 0;
                if (success >= 2) await onMatchSuccess();
            } else {
                fail++;
                success = 0;
                if (fail >= 3) {
                    statusText.textContent = "❌ Face mismatch";
                    videoContainer.className = "error";
                }
            }
        }, 300);


        function verifyDescriptor(descriptor) {
            const match = faceMatcher.findBestMatch(descriptor);
            if (match.label !== userId || match.distance >= 0.45) return false;

            if (matchMode === "both") {
                const d1 = faceapi.euclideanDistance(descriptor, baseDescriptor);
                const d2 = faceapi.euclideanDistance(descriptor, capturedDescriptor);
                return (d1 < 0.45 && d2 < 0.45);
            }
            return true;
        }


        async function onMatchSuccess() {
            matchFound = true;

            const snap = document.createElement("canvas");
            snap.width = video.videoWidth;
            snap.height = video.videoHeight;
            const ctx = snap.getContext("2d");
            ctx.translate(snap.width, 0);
            ctx.scale(-1, 1);
            ctx.drawImage(video, 0, 0);
            const img = snap.toDataURL("image/jpeg");

            capturedImage.src = img;
            capturedImage.style.display = "block";
            video.style.display = "none";
            statusText.textContent = `${userName}, Face matched ✅`;
            videoContainer.className = "success";

            await new Promise(r => setTimeout(r, 1500));

            Swal.fire({
                title: "Submitting attendance...",
                allowOutsideClick: false,
                didOpen: () => Swal.showLoading()
            });

            fetch("/TSUISLARS/Face/AttendanceData", {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ Type: entryType, ImageData: img })
            })
            .then(r => r.json())
            .then(data => {
                stopVideo();
                Swal.fire(
                    data.success ? "Thank you!" : "Error!",
                    data.success ? "Attendance Recorded" : data.message,
                    data.success ? "success" : "error"
                ).then(() => location.reload());
            });
        }
    }

    function getThreshold() {
        return navigator.userAgent.toLowerCase().includes("android") ? 0.5 : 0.45;
    }
}

</script>








<script>
let modelsLoaded = false;
let descriptorReady = false;

let baseDescriptor = null;
let capturedDescriptor = null;
let faceMatcher = null;

const cacheKey = `descriptor_${userId}`;
const modelCacheFlag = "faceModelsCached";

// ------------------ MODEL LOADER (SMART CACHED) ------------------ //

async function loadModelsOnce() {

    if (modelsLoaded) return; // already in memory

    const isCached = localStorage.getItem(modelCacheFlag);

    if (!isCached) {
        console.log("⬇️ Loading models from server for the FIRST time...");

        await Promise.all([
            faceapi.nets.tinyFaceDetector.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceLandmark68TinyNet.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceRecognitionNet.loadFromUri('/TSUISLARS/faceApi')
        ]);

        console.log("📦 Caching Models to IndexedDB...");
        await cacheModelsToIndexedDB();

        localStorage.setItem(modelCacheFlag, "true");
    } else {
        console.log("⚡ Loading models from IndexedDB (FAST)...");

        await Promise.all([
            faceapi.nets.tinyFaceDetector.load("indexedDB://tinyFaceDetector"),
            faceapi.nets.faceLandmark68TinyNet.load("indexedDB://landmarkNet"),
            faceapi.nets.faceRecognitionNet.load("indexedDB://recognitionNet")
        ]);
    }

    modelsLoaded = true;
    console.log("✔ Models Ready");
}

async function cacheModelsToIndexedDB() {
    await faceapi.nets.tinyFaceDetector.save("indexedDB://tinyFaceDetector");
    await faceapi.nets.faceLandmark68TinyNet.save("indexedDB://landmarkNet");
    await faceapi.nets.faceRecognitionNet.save("indexedDB://recognitionNet");
}

// ------------------ DESCRIPTOR CACHE SYSTEM ------------------ //

async function prepareDescriptors() {

    const latestVersion = await fetch(`/TSUISLARS/Face/GetImageVersion?userId=${userId}`)
        .then(r => r.json());

    const cached = JSON.parse(localStorage.getItem(cacheKey));

    if (!cached || cached.version !== latestVersion) {

        console.log("🆕 New image detected → regenerating descriptors");

        const encodedName = encodeURIComponent(userName);

        baseDescriptor = await loadDescriptor(`/TSUISLARS/Images/${userId}-${encodedName}.jpg?t=${latestVersion}`);
        capturedDescriptor = await loadDescriptor(`/TSUISLARS/Images/${userId}-Captured.jpg?t=${latestVersion}`);

        localStorage.setItem(cacheKey, JSON.stringify({
            version: latestVersion,
            base: baseDescriptor,
            captured: capturedDescriptor
        }));

    } else {
        console.log("♻ Loaded descriptors from cache");
        baseDescriptor = cached.base;
        capturedDescriptor = cached.captured;
    }

    descriptorReady = true;
}


    async function startFaceRecognition() {
        const video = document.getElementById("video");
        const canvas = document.getElementById("canvas");
        const capturedImage = document.getElementById("capturedImage");
        const EntryTypeInput = document.getElementById("EntryType");
        const statusText = document.getElementById("statusText");
        const videoContainer = document.getElementById("videoContainer");
        const entryType = document.getElementById("Entry").value;

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
            .then(stream => video.srcObject = stream)
            .catch(console.error);
        }

        function stopVideo() {
            const stream = video.srcObject;
            if (stream) stream.getTracks().forEach(track => track.stop());
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
                    return { success: false, reason: "Face not match with reference image." };
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
            } catch {}

            startVideo();
            Swal.close();

            if (!baseDescriptor && !capturedDescriptor) {
                statusText.textContent = "❌ No base image found. Please upload your image.";
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
            }

            let matchFound = false;
            let failCount = 0;
            let successCount = 0;

            setInterval(async () => {
                if (matchFound) return;

                const detections = await faceapi
                    .detectAllFaces(video, new faceapi.TinyFaceDetectorOptions({ inputSize: 160, scoreThreshold: 0.5 }))
                    .withFaceLandmarks(true)
                    .withFaceDescriptors();

                if (detections.length === 0) {
                    statusText.textContent = "No face detected";
         videoContainer.style.borderColor = "gray";
        videoContainer.classList.remove("success", "error");
videoContainer.classList.add("scanning");
                    failCount = 0; successCount = 0;
                    return;
                }

                if (detections.length > 1) {
                    statusText.textContent = "❌ Multiple faces detected.";
         videoContainer.style.borderColor = "red";
        videoContainer.classList.remove("scanning", "success");
videoContainer.classList.add("error");
                    failCount = 0; successCount = 0;
                    return;
                }

                const detection = detections[0];
                const result = verifyDescriptor(detection.descriptor, faceMatcher, matchMode, baseDescriptor, capturedDescriptor);

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
        videoContainer.classList.remove("scanning", "success");
videoContainer.classList.add("error");
                    }
                }
            }, 300);


async function onMatchSuccess(descriptor) {
    matchFound = true;

    const captureCanvas = document.createElement("canvas");
    captureCanvas.width = video.videoWidth;
    captureCanvas.height = video.videoHeight;

    const ctx = captureCanvas.getContext("2d");
    ctx.translate(captureCanvas.width, 0);
    ctx.scale(-1, 1);
    ctx.drawImage(video, 0, 0, captureCanvas.width, captureCanvas.height);

    const capturedDataURL = captureCanvas.toDataURL("image/jpeg");

    capturedImage.src = capturedDataURL;
    capturedImage.style.display = "block";
    video.style.display = "none";

    statusText.textContent = `${userName}, Face matched ✅`;
        videoContainer.style.borderColor = "green";
    videoContainer.classList.remove("scanning", "error");
    videoContainer.classList.add("success");

    await new Promise(resolve => setTimeout(resolve, 1500));


    Swal.fire({
        title: "Submitting...",
        text: "Please wait...",
        allowOutsideClick: false,
        showConfirmButton: false,
        didOpen: () => Swal.showLoading()
    });

    fetch("/TSUISLARS/Face/AttendanceData", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
            Type: entryType,
            ImageData: capturedDataURL
        })
    })
    .then(res => res.json())
    .then(data => {
        stopVideo();
        const now = new Date().toLocaleString();

        if (data.success) {
            Swal.fire("Thank you!", `Attendance Recorded.<br>${now}`, "success")
                .then(() => location.reload());
        } else {
            Swal.fire("Error!", data.message, "error")
                .then(() => location.reload());
        }
    })
    .catch(() => {
        Swal.fire("Error!", "Submission failed.", "error");
    });
}


            async function loadDescriptor(url) {
    try {
        const img = await faceapi.fetchImage(url);
        const detection = await faceapi
            .detectSingleFace(img, new faceapi.TinyFaceDetectorOptions({ inputSize: 160 }))
            .withFaceLandmarks(true)
            .withFaceDescriptor();

        return detection?.descriptor || null;
    } catch {
        return null;
    }
}

            function getThreshold() {
                const ua = navigator.userAgent.toLowerCase();
                return ua.includes("android") ? 0.5 : 0.45;
            }
        }
    }
</script>
