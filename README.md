<script>

let modelsLoaded = false;
let baseDescriptor = null;
let capturedDescriptor = null;
let faceMatcher = null;
let descriptorVersionKey = `descriptor_${userId}`;

// ------------------ MODEL LOAD (ONE TIME) ------------------ //

window.addEventListener("load", async () => {
    console.log("Loading face recognition models...");
    await Promise.all([
        faceapi.nets.tinyFaceDetector.loadFromUri('/TSUISLARS/faceApi'),
        faceapi.nets.faceLandmark68TinyNet.loadFromUri('/TSUISLARS/faceApi'),
        faceapi.nets.faceRecognitionNet.loadFromUri('/TSUISLARS/faceApi')
    ]);

    modelsLoaded = true;
    console.log("✔ Models loaded successfully");
});

// ------------------ DESCRIPTOR LOADER WITH VERSION CHECK ------------------ //

async function loadDescriptorsIfNeeded() {
    const timestamp = await fetch(`/TSUISLARS/Face/GetImageVersion?userId=${userId}`)
        .then(r => r.json());

    const cached = JSON.parse(localStorage.getItem(descriptorVersionKey));

    if (!cached || cached.version !== timestamp) {

        console.log("📌 New or missing descriptor → generating...");

        const safeName = userName.replace(/\s+/g, "%20");

        baseDescriptor = await loadDescriptor(`/TSUISLARS/Images/${userId}-${safeName}.jpg?t=${timestamp}`);
        capturedDescriptor = await loadDescriptor(`/TSUISLARS/Images/${userId}-Captured.jpg?t=${timestamp}`);

        // Store in cache
        localStorage.setItem(descriptorVersionKey, JSON.stringify({
            version: timestamp,
            base: baseDescriptor,
            captured: capturedDescriptor
        }));

    } else {
        console.log("♻ Using cached descriptor.");
        baseDescriptor = cached.base;
        capturedDescriptor = cached.captured;
    }
}

// ------------------ MAIN START FUNCTION ------------------ //

async function startFaceRecognition() {

    Swal.fire({
        title: '⏳ Loading...',
        text: 'Preparing face recognition...',
        allowOutsideClick: false,
        didOpen: () => Swal.showLoading()
    });

    // Wait for models to be ready
    while (!modelsLoaded) {
        await new Promise(resolve => setTimeout(resolve, 100));
    }

    await loadDescriptorsIfNeeded();

    Swal.close();

    initFaceRecognition(); // move into your existing logic
}

// ------------------ ORIGINAL LOGIC (UNCHANGED) ------------------ //

async function loadDescriptor(imageUrl) {
    try {
        const img = await faceapi.fetchImage(imageUrl);
        const detection = await faceapi
            .detectSingleFace(img, new faceapi.TinyFaceDetectorOptions({ inputSize: 160 }))
            .withFaceLandmarks(true)
            .withFaceDescriptor();

        return detection?.descriptor || null;
    } catch (e) {
        return null;
    }
}

function getThreshold() {
    const ua = navigator.userAgent.toLowerCase();
    return ua.includes("android") ? 0.5 : 0.45;
}

async function initFaceRecognition() {

    const video = document.getElementById("video");
    const canvas = document.getElementById("canvas");
    const capturedImage = document.getElementById("capturedImage");
    const EntryTypeInput = document.getElementById("EntryType");
    const statusText = document.getElementById("statusText");
    const videoContainer = document.getElementById("videoContainer");
    const entryType = document.getElementById("Entry").value;

    function startVideo() {
        navigator.mediaDevices.getUserMedia({
            video: { facingMode: "user", width: { ideal: 640 }, height: { ideal: 480 } }
        }).then(stream => video.srcObject = stream);
    }

    function stopVideo() {
        const stream = video.srcObject;
        if (stream) stream.getTracks().forEach(track => track.stop());
        video.srcObject = null;
    }

    // Build Face Matcher
    if (baseDescriptor && capturedDescriptor) {
        faceMatcher = new faceapi.FaceMatcher(
            [new faceapi.LabeledFaceDescriptors(userId, [baseDescriptor, capturedDescriptor])],
            getThreshold()
        );
    } else if (baseDescriptor) {
        faceMatcher = new faceapi.FaceMatcher(
            [new faceapi.LabeledFaceDescriptors(userId, [baseDescriptor])],
            getThreshold()
        );
    }

    startVideo();

    let matchFound = false;
    let failCount = 0;
    let successCount = 0;

    setInterval(async () => {
        if (matchFound) return;

        const detections = await faceapi
            .detectAllFaces(video, new faceapi.TinyFaceDetectorOptions({ inputSize: 160, scoreThreshold: 0.5 }))
            .withFaceLandmarks(true)
            .withFaceDescriptors();

        if (detections.length !== 1) {
            statusText.textContent = detections.length === 0 ? "No face detected" : "❌ Multiple faces detected.";
            videoContainer.classList.remove("success", "error");
            videoContainer.classList.add(detections.length === 0 ? "scanning" : "error");
            failCount = successCount = 0;
            return;
        }

        const match = faceMatcher.findBestMatch(detections[0].descriptor);

        if (match.label === userId && match.distance <= getThreshold()) {
            successCount++;
            if (successCount >= 2) onMatchSuccess(detections[0].descriptor);
        } else {
            failCount++;
            if (failCount >= 3) {
                statusText.textContent = "❌ Face not match";
                videoContainer.classList.remove("scanning", "success");
                videoContainer.classList.add("error");
            }
            successCount = 0;
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
        ctx.drawImage(video, 0, 0, captureCanvas.width, captureCanvas.height);

        const capturedDataURL = captureCanvas.toDataURL("image/jpeg");

        capturedImage.src = capturedDataURL;
        capturedImage.style.display = "block";
        video.style.display = "none";

        statusText.textContent = `${userName}, Face matched ✅`;
        videoContainer.classList.add("success");

        await new Promise(resolve => setTimeout(resolve, 1500));

        Swal.fire({ title: "Submitting...", text: "Please wait...", allowOutsideClick: false, showConfirmButton: false, didOpen: () => Swal.showLoading() });

        fetch("/TSUISLARS/Face/AttendanceData", {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({ Type: entryType, ImageData: capturedDataURL })
        })
        .then(res => res.json())
        .then(data => {
            stopVideo();
            const now = new Date().toLocaleString();
            Swal.fire(data.success ? "Thank you!" : "Error!", data.success ? `Attendance Recorded.<br>${now}` : data.message, data.success ? "success" : "error")
                .then(() => location.reload());
        });
    }
}

</script>





this is my js 

<script>
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

            function getThreshold() {
                const ua = navigator.userAgent.toLowerCase();
                return ua.includes("android") ? 0.5 : 0.45;
            }
        }
    }
</script>


i takes too much time to load when loading Face recognition model as well as the both images. i want instantly load all the models and Images 
