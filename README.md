my new Logic 
<script>
let descriptorCachePrefix = "face_descriptor_";
let descriptorVersionPrefix = "face_desc_ver_";

let modelsLoaded = false;

async function startFaceRecognition() {
    const video = document.getElementById("video");
    const capturedImage = document.getElementById("capturedImage");
    const statusText = document.getElementById("statusText");
    const videoContainer = document.getElementById("videoContainer");
    const entryType = document.getElementById("Entry").value;

    Swal.fire({
        title: 'Please wait...',
        text: 'Preparing face recognition.',
        allowOutsideClick: false,
        didOpen: () => Swal.showLoading()
    });


    if (!modelsLoaded) {
        await Promise.all([
            faceapi.nets.tinyFaceDetector.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceLandmark68TinyNet.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceRecognitionNet.loadFromUri('/TSUISLARS/faceApi')
        ]);

        localStorage.setItem("modelsCached", "true");
        modelsLoaded = true;
    }


    if (!localStorage.getItem("dummyWarmupDone")) {
        const dummy = document.createElement("canvas");
        dummy.width = 160; dummy.height = 160;
        await faceapi.detectSingleFace(dummy, new faceapi.TinyFaceDetectorOptions());
        localStorage.setItem("dummyWarmupDone", "true");
    }

    initFaceRecognition();


    async function getDescriptor(imageUrl, keyName, versionKeyName) {
        let serverTimestamp = await fetch(imageUrl, { method: 'HEAD' })
            .then(r => r.headers.get("last-modified"))
            .catch(() => null);

        let serverVersion = serverTimestamp ? new Date(serverTimestamp).getTime() : 0;
        let storedVersion = parseInt(localStorage.getItem(versionKeyName) || 0);
        let cached = localStorage.getItem(keyName);

        if (cached && storedVersion === serverVersion) {
            return Float32Array.from(JSON.parse(cached));
        }


        const img = await faceapi.fetchImage(imageUrl);
        const detection = await faceapi
            .detectSingleFace(img, new faceapi.TinyFaceDetectorOptions({ inputSize: 160 }))
            .withFaceLandmarks(true)
            .withFaceDescriptor();

        if (detection?.descriptor) {
            localStorage.setItem(keyName, JSON.stringify(Array.from(detection.descriptor)));
            localStorage.setItem(versionKeyName, serverVersion);
            return detection.descriptor;
        }
        return null;
    }


    async function initFaceRecognition() {
        const safeUser = userName.replace(/\s+/g, "%20");

        const baseImg = `/TSUISLARS/Images/${userId}-${safeUser}.jpg`;
        const capImg = `/TSUISLARS/Images/${userId}-Captured.jpg`;

        const baseDesc = await getDescriptor(
            baseImg,
            descriptorCachePrefix + userId + "_base",
            descriptorVersionPrefix + userId + "_base"
        );

        const capDesc = await getDescriptor(
            capImg,
            descriptorCachePrefix + userId + "_captured",
            descriptorVersionPrefix + userId + "_captured"
        );

        startVideo();
        Swal.close();

        if (!baseDesc && !capDesc) {
            statusText.textContent = "❌ No base image found. Please upload your image.";
            return;
        }

        let matcher = null;
        let mode = "";

        if (baseDesc && capDesc) {
            matcher = new faceapi.FaceMatcher(
                [new faceapi.LabeledFaceDescriptors(userId, [baseDesc, capDesc])],
                getThreshold()
            );
            mode = "both";
        } else if (baseDesc) {
            matcher = new faceapi.FaceMatcher(
                [new faceapi.LabeledFaceDescriptors(userId, [baseDesc])],
                getThreshold()
            );
            mode = "baseOnly";
        }

        let matchFound = false, fail = 0, success = 0;

        setInterval(async () => {
            if (matchFound) return;

            const detections = await faceapi
                .detectAllFaces(video, new faceapi.TinyFaceDetectorOptions({ inputSize: 160, scoreThreshold: 0.5 }))
                .withFaceLandmarks(true)
                .withFaceDescriptors();

            if (detections.length !== 1) {
                fail = 0; success = 0;
                statusText.textContent = detections.length === 0 ? "No face detected" : "❌ Multiple faces detected.";
                videoContainer.style.borderColor = detections.length === 0 ? "gray" : "red";
                return;
            }

            const res = matcher.findBestMatch(detections[0].descriptor);

            if (res.label === userId && res.distance < 0.45) success++;
            else { success = 0; fail++; }

            if (success >= 2) onMatchSuccess();
            if (fail >= 3)
                statusText.textContent = "❌ Face not match";
                videoContainer.style.borderColor = "red";
        }, 300);

        /* -------------------------------------- */
        async function onMatchSuccess() {
            matchFound = true;

            const captureCanvas = document.createElement("canvas");
            captureCanvas.width = video.videoWidth;
            captureCanvas.height = video.videoHeight;

            const ctx = captureCanvas.getContext("2d");
            ctx.translate(captureCanvas.width, 0);
            ctx.scale(-1, 1);
            ctx.drawImage(video, 0, 0, captureCanvas.width, captureCanvas.height);

            const dataURL = captureCanvas.toDataURL("image/jpeg");

            capturedImage.src = dataURL;
            capturedImage.style.display = "block";
            video.style.display = "none";
            statusText.textContent = `${userName}, Face Matched ✅`;
            videoContainer.style.borderColor = "green";


            await new Promise(r => setTimeout(r, 1200));

            Swal.fire({ title: "Submitting...", allowOutsideClick: false, showConfirmButton: false, didOpen: () => Swal.showLoading() });

            fetch("/TSUISLARS/Face/AttendanceData", {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ Type: entryType, ImageData: dataURL })
            })
                .then(res => res.json())
                .then(data => {
                    stopVideo();
                    const now = new Date().toLocaleString();
                    Swal.fire(
                        data.success ? "Thank you!" : "Error!",
                        data.success ? `Attendance Recorded.<br>${now}` : data.message,
                        data.success ? "success" : "error"
                    ).then(() => location.reload());
                })
                .catch(() => Swal.fire("Error!", "Submission failed.", "error"));
        }
    }

    function startVideo() {
        navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } })
            .then(s => video.srcObject = s)
            .catch(console.error);
    }

    function stopVideo() {
        const s = video.srcObject;
        if (s) s.getTracks().forEach(t => t.stop());
        video.srcObject = null;
    }

    function getThreshold() {
        return navigator.userAgent.toLowerCase().includes("android") ? 0.5 : 0.45;
    }
}
</script>

old logic 

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



i want same messaging like the old part . logic will be same for new but message with borders like the old one because new one has issues that face matched but red border showing 
