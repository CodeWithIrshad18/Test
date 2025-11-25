<script>
let modelsLoaded = false;                     // <-- Load ONCE only
let descriptorCachePrefix = "face_descriptor_";
let descriptorVersionPrefix = "face_desc_ver_";

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

    /* -----------------------------------------------------------------
       1️⃣ FACE MODEL LOADING ONLY ONCE
    ----------------------------------------------------------------- */
    if (!modelsLoaded) {
        await Promise.all([
            faceapi.nets.tinyFaceDetector.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceLandmark68TinyNet.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceRecognitionNet.loadFromUri('/TSUISLARS/faceApi')
        ]);
        modelsLoaded = true;
    }

    const dummy = document.createElement("canvas");
    dummy.width = 160; dummy.height = 160;
    await faceapi.detectSingleFace(dummy, new faceapi.TinyFaceDetectorOptions());
    initFaceRecognition();


    /* --------------------------------------------------------------
        2️⃣ LOAD + CACHE DESCRIPTOR WITH VERSION BASED ON TIMESTAMP
    -------------------------------------------------------------- */
    async function getDescriptor(imageUrl, keyName, versionKeyName) {
        let serverTimestamp = await fetch(imageUrl, { method: 'HEAD' })
            .then(resp => resp.headers.get("last-modified"))
            .catch(() => null);

        // Convert string "last-modified" to epoch time
        let serverVersion = serverTimestamp ? new Date(serverTimestamp).getTime() : 0;

        let storedVersion = parseInt(localStorage.getItem(versionKeyName) || 0);
        let cachedDesc = localStorage.getItem(keyName);

        if (cachedDesc && storedVersion === serverVersion) {
            return Float32Array.from(JSON.parse(cachedDesc)); // return cached
        }

        // compute new descriptor because server version changed
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

    /* -------------------------------------------------------------- */
    async function initFaceRecognition() {
        const safeUserName = userName.replace(/\s+/g, "%20");

        // These URLs dynamically change only if file was updated
        const baseImg = `/TSUISLARS/Images/${userId}-${safeUserName}.jpg`;
        const capImg = `/TSUISLARS/Images/${userId}-Captured.jpg`;

        const baseDescriptor = await getDescriptor(
            baseImg,
            descriptorCachePrefix + userId + "_base",
            descriptorVersionPrefix + userId + "_base"
        );

        const capturedDescriptor = await getDescriptor(
            capImg,
            descriptorCachePrefix + userId + "_captured",
            descriptorVersionPrefix + userId + "_captured"
        );

        startVideo();
        Swal.close();

        if (!baseDescriptor && !capturedDescriptor) {
            statusText.textContent = "❌ No base image found. Please upload your image.";
            return;
        }

        let faceMatcher = null;
        let mode = "";

        if (baseDescriptor && capturedDescriptor) {
            faceMatcher = new faceapi.FaceMatcher(
                [new faceapi.LabeledFaceDescriptors(userId, [baseDescriptor, capturedDescriptor])],
                getThreshold()
            );
            mode = "both";
        } else if (baseDescriptor) {
            faceMatcher = new faceapi.FaceMatcher(
                [new faceapi.LabeledFaceDescriptors(userId, [baseDescriptor])],
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

            const descriptor = detections[0].descriptor;
            const match = faceMatcher.findBestMatch(descriptor);

            if (match.label === userId && match.distance < 0.45) success++;
            else { success = 0; fail++; }

            if (success >= 2) onMatchSuccess(descriptor);
            if (fail >= 3) {
                statusText.textContent = "❌ Face not match";
                videoContainer.style.borderColor = "red";
            }
        }, 300);

        /* ------------------------------------ */
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
            statusText.textContent = `${userName}, Face Matched ✅`;
            videoContainer.style.borderColor = "green";

            await new Promise(r => setTimeout(r, 1500));

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
                body: JSON.stringify({ Type: entryType, ImageData: capturedDataURL })
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

    /* --------------------------------------- */
    function startVideo() {
        navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } })
            .then(stream => video.srcObject = stream)
            .catch(console.error);
    }

    function stopVideo() {
        const stream = video.srcObject;
        if (stream) stream.getTracks().forEach(t => t.stop());
        video.srcObject = null;
    }

    function getThreshold() {
        const ua = navigator.userAgent.toLowerCase();
        return ua.includes("android") ? 0.5 : 0.45;
    }
}
</script>






please review my full code 
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
</script>

<script>

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

    await loadModelsOnce();

    await prepareDescriptors();

    Swal.close();

    initFaceRecognition();

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

            // const lat = 22.796898;
            // const lon = 86.18421;

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

               if (!window.faceRecognitionStarted) {
        window.faceRecognitionStarted = true;
        startFaceRecognition();
    }
                  
            } 
            
          

            else {
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

                 statusText.textContent = "Please refresh if your are in allowed location...";
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

            statusText.textContent = "Please refresh if your are in allowed location...";
        }
    }

    function getCurrentPosition(options) {
        return new Promise((resolve, reject) => {
            navigator.geolocation.getCurrentPosition(resolve, reject, options);
        });
    }

    function calculateDistance(lat1, lon1, lat2, lon2) {
        const R = 6371000; 
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
</script>
