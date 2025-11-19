async function onMatchSuccess(descriptor) {
    matchFound = true;

    statusText.textContent = `${userName}, Face matched ✅`;
    videoContainer.style.borderColor = "green";
    videoContainer.className = "success";

    // -------- 1️⃣ ANTI-BLUR STABILIZER --------
    statusText.textContent = "Hold still... capturing best clear frame.";

    let stableCount = 0;
    let lastBox = null;

    for (let i = 0; i < 10; i++) {
        const det = await faceapi
            .detectSingleFace(video, new faceapi.TinyFaceDetectorOptions({ inputSize: 224, scoreThreshold: 0.5 }))
            .withFaceLandmarks()
            .withFaceDescriptor();

        if (!det) continue;

        const box = det.detection.box;

        if (lastBox) {
            const movement =
                Math.abs(box.x - lastBox.x) +
                Math.abs(box.y - lastBox.y) +
                Math.abs(box.width - lastBox.width) +
                Math.abs(box.height - lastBox.height);

            // Movement threshold (tuneable)
            if (movement < 12) {
                stableCount++;
            } else {
                stableCount = 0;
            }
        }
        lastBox = box;

        if (stableCount >= 3) break;

        await new Promise(res => setTimeout(res, 100));
    }

    // If not stable, still capture best available frame
    statusText.textContent = "Capturing clear frame...";

    // -------- 2️⃣ HIGH-CLARITY ENHANCED CAPTURE --------
    const captureCanvas = document.createElement("canvas");

    // Capture at double resolution for sharpness
    captureCanvas.width = video.videoWidth * 1.5;
    captureCanvas.height = video.videoHeight * 1.5;

    const ctx = captureCanvas.getContext("2d");

    ctx.translate(captureCanvas.width, 0);
    ctx.scale(-1, 1);

    // Draw higher-res frame
    ctx.drawImage(video, 0, 0, captureCanvas.width, captureCanvas.height);

    // Apply mild clarity enhancement
    ctx.filter = "contrast(115%) brightness(110%)";
    ctx.drawImage(captureCanvas, 0, 0);

    // Get final image
    const capturedDataURL = captureCanvas.toDataURL("image/jpeg", 0.9);

    // Freeze preview
    capturedImage.src = capturedDataURL;
    capturedImage.style.display = "block";
    video.style.display = "none";

    statusText.textContent = "Face matched ✓ — Saving in 2.5 seconds...";

    // -------- 3️⃣ WAIT EXACT 2.5 SECONDS --------
    await new Promise(resolve => setTimeout(resolve, 2500));

    // -------- 4️⃣ SUBMIT ATTENDANCE --------
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





<div id="timerScreen" style="display:none; text-align:center; margin-top:40px;">
    <h2>Please wait...</h2>
    <p>You can punch again after.</p>
    <h1 id="countdownTimer" style="font-size:48px; font-weight:bold;"></h1>
    
</div>


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

    const unlockTime = new Date(lastPunchDateTime.getTime() + 2 * 60000);
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

    document.getElementById("timerScreen").style.display = "block";

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
