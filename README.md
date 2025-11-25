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

// ------------------ MAIN ENTRY (AFTER LOCATION PASS) ------------------ //

async function startFaceRecognition() {

    if (!descriptorReady) {
        Swal.fire({
            title: '⏳ Preparing Face Recognition...',
            text: 'Loading resources...',
            allowOutsideClick: false,
            didOpen: () => Swal.showLoading()
        });
    }

    await loadModelsOnce();
    await prepareDescriptors();

    Swal.close();
    initFaceRecognition();
}

// ------------------ Descriptor Loader ------------------ //

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

// ------------------ Recognition Logic (UNCHANGED) ------------------ //

function getThreshold() {
    return navigator.userAgent.toLowerCase().includes("android") ? 0.5 : 0.45;
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
        navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } })
            .then(stream => video.srcObject = stream);
    }

    function stopVideo() {
        const stream = video.srcObject;
        if (stream) stream.getTracks().forEach(track => track.stop());
        video.srcObject = null;
    }

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
            .detectAllFaces(video, new faceapi.TinyFaceDetectorOptions({ inputSize: 160 }))
            .withFaceLandmarks(true)
            .withFaceDescriptors();

        if (detections.length !== 1) {
            statusText.textContent = "Please align your face...";
            failCount = successCount = 0;
            return;
        }

        const match = faceMatcher.findBestMatch(detections[0].descriptor);

        if (match.label === userId && match.distance <= getThreshold()) {
            successCount++;
            if (successCount >= 2) onSuccess();
        } else {
            failCount++;
        }

    }, 300);

    async function onSuccess() {

        matchFound = true;

        const screenshot = document.createElement("canvas");
        screenshot.width = video.videoWidth;
        screenshot.height = video.videoHeight;

        const ctx = screenshot.getContext("2d");
        ctx.translate(screenshot.width, 0);
        ctx.scale(-1, 1);
        ctx.drawImage(video, 0, 0);

        const imgBase64 = screenshot.toDataURL("image/jpeg");

        statusText.textContent = `${userName}, Face Matched ✅`;

        await new Promise(resolve => setTimeout(resolve, 1500));

        Swal.fire({
            title: "Submitting Attendance...",
            allowOutsideClick: false,
            didOpen: () => Swal.showLoading()
        });

        fetch("/TSUISLARS/Face/AttendanceData", {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({ Type: entryType, ImageData: imgBase64 })
        })
        .then(r => r.json())
        .then(res => {
            stopVideo();

            Swal.fire(res.success ? "Success!" : "Error",
                res.success ? "Attendance recorded." : res.message,
                res.success ? "success" : "error"
            ).then(() => location.reload());
        });
    }
}

</script>




let modelsLoaded = false;
let descriptorReady = false;

let baseDescriptor = null;
let capturedDescriptor = null;
let faceMatcher = null;

const cacheKey = `descriptor_${userId}`;

// --------------- MODEL LOADING (ONLY ONCE PER SESSION) ---------------- //

async function loadModelsOnce() {
    if (modelsLoaded) return;

    await Promise.all([
        faceapi.nets.tinyFaceDetector.loadFromUri('/TSUISLARS/faceApi'),
        faceapi.nets.faceLandmark68TinyNet.loadFromUri('/TSUISLARS/faceApi'),
        faceapi.nets.faceRecognitionNet.loadFromUri('/TSUISLARS/faceApi')
    ]);

    modelsLoaded = true;
    console.log("✔ Models Loaded");
}

// --------------- CHECK NEW IMAGE + DESCRIPTOR CACHE ---------------- //

async function prepareDescriptors() {
    const latestVersion = await fetch(`/TSUISLARS/Face/GetImageVersion?userId=${userId}`)
        .then(r => r.json());

    const cached = JSON.parse(localStorage.getItem(cacheKey));

    // If user uploaded new image → regenerate descriptors
    if (!cached || cached.version !== latestVersion) {

        console.log("📌 New image detected → generating descriptor");

        const encodedName = encodeURIComponent(userName);

        baseDescriptor = await loadDescriptor(`/TSUISLARS/Images/${userId}-${encodedName}.jpg?t=${latestVersion}`);
        capturedDescriptor = await loadDescriptor(`/TSUISLARS/Images/${userId}-Captured.jpg?t=${latestVersion}`);

        localStorage.setItem(cacheKey, JSON.stringify({
            version: latestVersion,
            base: baseDescriptor,
            captured: capturedDescriptor
        }));

    } else {
        console.log("♻ Using Cached Descriptors");
        baseDescriptor = cached.base;
        capturedDescriptor = cached.captured;
    }

    descriptorReady = true;
}

// ----------------- MAIN START FUNCTION ----------------- //

async function startFaceRecognition() {

    // 🔥 Only show loader ONCE, NOT every time
    if (!descriptorReady) {
        Swal.fire({
            title: "Preparing Face Recognition...",
            text: "Please wait...",
            allowOutsideClick: false,
            didOpen: () => Swal.showLoading()
        });
    }

    await loadModelsOnce();
    await prepareDescriptors();

    Swal.close();

    // Start your original logic
    initFaceRecognition();
}

// ------------------ UNCHANGED PART ------------------ //

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





I have this 3 Js , first Location check then timer and then face recognition . code according to this 

<script>
document.addEventListener("DOMContentLoaded", function () {

    const lastPunch = "@ViewBag.LatestPunchTime";  

    if (!lastPunch || lastPunch.trim() === "") {
        showMainUI();
        OnOff();
        return;
    }

    const today = new Date();
    const [hh, mm] = lastPunch.split(":").map(Number);

    const lastPunchDateTime = new Date(
        today.getFullYear(),
        today.getMonth(),
        today.getDate(),
        hh,
        mm,
        0
    );

    const unlockTime = new Date(lastPunchDateTime.getTime() + 5 * 60000);
    const now = new Date();

    if (now < unlockTime) {
        startCountdown(unlockTime);
    } else {
        showMainUI();
        OnOff();
    }

});

function startCountdown(unlockTime) {

    document.getElementById("mainFormContainer").style.display = "none";

    document.getElementById("timerScreen").style.display = "flex";

    const timerLabel = document.getElementById("countdownTimer");

    const interval = setInterval(() => {
        const now = new Date();
        const diff = unlockTime - now;

        if (diff <= 0) {
            clearInterval(interval);

            showMainUI();

            OnOff();

            return;
        }

        const minutes = Math.floor(diff / 60000);
        const seconds = Math.floor((diff % 60000) / 1000);

        timerLabel.textContent =
            `${minutes.toString().padStart(2, "0")}:${seconds
                .toString().padStart(2, "0")}`;

    }, 1000);
}

function showMainUI() {
    document.getElementById("timerScreen").style.display = "none";
    document.getElementById("mainFormContainer").style.display = "block";
}
</script>

<script>
    const userId = '@ViewBag.UserId';
    const userName = '@ViewBag.UserName';
</script>

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
                  await startFaceRecognition();
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

