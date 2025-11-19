
matchDone = true;
videoContainer.className = "success";
statusText.textContent = `${userName}, Face matched ✔\nPreparing best image...`;

// 1️⃣ CAPTURE IMAGE IMMEDIATELY
const captureCanvas = document.createElement("canvas");
captureCanvas.width = video.videoWidth;
captureCanvas.height = video.videoHeight;

let ctx = captureCanvas.getContext("2d");
ctx.translate(captureCanvas.width, 0);
ctx.scale(-1, 1);
ctx.drawImage(video, 0, 0);

const finalImageData = captureCanvas.toDataURL("image/jpeg");

// 2️⃣ DELAY 2.5 SECONDS BEFORE STORING
setTimeout(() => {
    submitAttendance(finalImageData);
}, 2500); // 2500 ms delay

submitAttendance(finalImageData);

function submitAttendance(imageData) {

    Swal.fire({
        title: "Submitting Attendance...",
        allowOutsideClick: false,
        showConfirmButton: false,
        didOpen: () => Swal.showLoading()
    });

    fetch("/TSUISLARS/Face/AttendanceData", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
            Type: entryType,
            ImageData: imageData  // ← use pre-captured image
        })
    })
    .then(res => res.json())
    .then(data => { ... });
}


<script>
async function startFaceRecognition() {

    const video = document.getElementById("video");
    const canvas = document.getElementById("canvas");
    const statusText = document.getElementById("statusText");
    const videoContainer = document.getElementById("videoContainer");
    const entryType = document.getElementById("Entry").value;

    Swal.fire({
        title: 'Please wait...',
        text: 'Preparing face recognition...',
        allowOutsideClick: false,
        didOpen: () => Swal.showLoading()
    });

    // Load face-api models
    await Promise.all([
        faceapi.nets.tinyFaceDetector.loadFromUri('/TSUISLARS/faceApi'),
        faceapi.nets.faceLandmark68TinyNet.loadFromUri('/TSUISLARS/faceApi'),
        faceapi.nets.faceRecognitionNet.loadFromUri('/TSUISLARS/faceApi'),
    ]);

    Swal.close();

    startVideo();

    function startVideo() {
        navigator.mediaDevices.getUserMedia({
            video: { facingMode: "user" }
        }).then(stream => video.srcObject = stream);
    }

    function stopVideo() {
        if (video.srcObject) {
            video.srcObject.getTracks().forEach(t => t.stop());
            video.srcObject = null;
        }
    }

    // Load reference descriptors
    async function loadDescriptor(url) {
        try {
            let img = await faceapi.fetchImage(url);
            let d = await faceapi
                .detectSingleFace(img, new faceapi.TinyFaceDetectorOptions({ inputSize: 160 }))
                .withFaceLandmarks(true)
                .withFaceDescriptor();
            return d?.descriptor || null;
        } catch {
            return null;
        }
    }

    const safeName = userName.replace(/\s+/g, "%20");
    const ts = Date.now();

    const baseURL = `/TSUISLARS/Images/${userId}-${safeName}.jpg?t=${ts}`;
    const capturedURL = `/TSUISLARS/Images/${userId}-Captured.jpg?t=${ts}`;

    const baseDesc = await loadDescriptor(baseURL);
    const capDesc = await loadDescriptor(capturedURL);

    if (!baseDesc && !capDesc) {
        statusText.textContent = "❌ No registered face found.";
        return;
    }

    const faceMatcher = new faceapi.FaceMatcher(
        [new faceapi.LabeledFaceDescriptors(userId, [baseDesc, capDesc].filter(Boolean))],
        getThreshold()
    );

    let matchDone = false;

    setInterval(async () => {
        if (matchDone) return;

        const detections = await faceapi
            .detectAllFaces(video, new faceapi.TinyFaceDetectorOptions({ inputSize: 160, scoreThreshold: 0.5 }))
            .withFaceLandmarks(true)
            .withFaceDescriptors();

        if (detections.length !== 1) {
            statusText.textContent = detections.length === 0 ? "No face detected" : "Multiple faces detected";
            videoContainer.className = "error";
            return;
        }

        const bestMatch = faceMatcher.findBestMatch(detections[0].descriptor);

        if (bestMatch.label === userId && bestMatch.distance < getThreshold()) {
            matchDone = true;
            videoContainer.className = "success";
            statusText.textContent = `${userName}, Face matched ✔`;

            submitAttendance();
        } else {
            videoContainer.className = "error";
            statusText.textContent = "❌ Face not matched";
        }

    }, 300);

    function submitAttendance() {

        // Capture live image
        const capCanvas = document.createElement("canvas");
        capCanvas.width = video.videoWidth;
        capCanvas.height = video.videoHeight;

        let ctx = capCanvas.getContext("2d");
        ctx.translate(capCanvas.width, 0);
        ctx.scale(-1, 1);
        ctx.drawImage(video, 0, 0);

        let imgData = capCanvas.toDataURL("image/jpeg");

        Swal.fire({
            title: "Submitting Attendance...",
            allowOutsideClick: false,
            showConfirmButton: false,
            didOpen: () => Swal.showLoading()
        });

        fetch("/TSUISLARS/Face/AttendanceData", {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({
                Type: entryType,
                ImageData: imgData
            })
        })
        .then(res => res.json())
        .then(data => {
            stopVideo();
            let now = new Date().toLocaleString();

            if (data.success) {
                Swal.fire("Success!", `Attendance Recorded<br>${now}`, "success")
                    .then(() => location.reload());
            } else {
                Swal.fire("Error!", data.message, "error")
                    .then(() => location.reload());
            }
        })
        .catch(() => {
            Swal.fire("Error!", "Failed to submit attendance.", "error");
        });
    }

    function getThreshold() {
        return navigator.userAgent.toLowerCase().includes("android") ? 0.50 : 0.45;
    }
}
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
            } catch (err) {
                console.warn("Error loading descriptors:", err);
            }

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
            } else {
                statusText.textContent = "⚠️ No base image found. Please upload your image.";
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
        videoContainer.classList.remove("success", "error");
videoContainer.classList.add("scanning");
        failCount = 0; successCount = 0;
        return;
    }

    if (detections.length > 1) {
        statusText.textContent = "❌ Multiple faces detected. Please ensure only one face is visible.";
         videoContainer.style.borderColor = "red";
        videoContainer.classList.remove("scanning", "success");
videoContainer.classList.add("error");
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
            videoContainer.classList.remove("scanning", "success");
videoContainer.classList.add("error");
            logFailure();
        }
    }
}, 300);

           async function onMatchSuccess(descriptor) {
    matchFound = true;

    statusText.textContent = `${userName}, Face matched ✅`;
    videoContainer.style.borderColor = "green";
    videoContainer.classList.remove("scanning", "error");
    videoContainer.classList.add("success");

    const captureCanvas = document.createElement("canvas");
    captureCanvas.width = video.videoWidth;
    captureCanvas.height = video.videoHeight;

    const ctx = captureCanvas.getContext("2d");
    ctx.translate(captureCanvas.width, 0);
    ctx.scale(-1, 1);
    ctx.drawImage(video, 0, 0, captureCanvas.width, captureCanvas.height);

    const capturedDataURL = captureCanvas.toDataURL("image/jpeg");

    const finalDetection = await faceapi
        .detectSingleFace(captureCanvas, new faceapi.TinyFaceDetectorOptions({ inputSize: 160 }))
        .withFaceLandmarks(true)
        .withFaceDescriptor();

    if (!finalDetection) {
        statusText.textContent = "❌ Face lost during capture. Try again.";
        matchFound = false;
        return;
    }

    const finalResult = verifyDescriptor(
        finalDetection.descriptor,
        faceMatcher,
        matchMode,
        baseDescriptor,
        capturedDescriptor
    );

    if (!finalResult.success) {
        statusText.textContent = "❌ " + finalResult.reason;
        matchFound = false;
        return;
    }

    Swal.fire({
        title: "Please wait...",
        text: "Submitting attendance...",
        allowOutsideClick: false,
        showConfirmButton: false,
        didOpen: () => Swal.showLoading()
    });

    fetch("/TSUISLARS/Face/AttendanceData", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
            Type: document.getElementById("Entry").value, 
            ImageData: capturedDataURL
        })
    })
    .then(res => res.json())
    .then(data => {
        stopVideo();
        if (data.success) {
            Swal.fire("Success!", "Attendance Recorded.", "success")
                .then(() => location.reload());
        } else {
            Swal.fire("Error!", data.message, "error")
                .then(() => location.reload());
        }
    })
    .catch(() => {
        Swal.fire("Error", "Failed to submit attendance.", "error");
    });
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
            videoContainer.classList.remove("scanning", "success");
videoContainer.classList.add("error");
            return resetToRetry();
        }

     
        const result = verifyDescriptor(detection.descriptor, faceMatcher, matchMode, baseDescriptor, capturedDescriptor);

        if (!result.success) {
            statusText.textContent = "❌ " + result.reason;
             videoContainer.style.borderColor = "red";
            videoContainer.classList.remove("scanning", "success");
videoContainer.classList.add("error");
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
