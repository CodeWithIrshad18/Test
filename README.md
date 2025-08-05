this is my code of js , in this I want that after capture of image button is showing when user click on button again check the face after verification then data is stored 

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

    <div class="mt-5 form-group">
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
    </div>


</form>

<script>
    const userId = '@ViewBag.UserId';
    const userName = '@ViewBag.UserName';
</script>

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

        if (punchInButton) punchInButton.style.display = "none";
        if (punchOutButton) punchOutButton.style.display = "none";

        await Promise.all([
            faceapi.nets.tinyFaceDetector.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceLandmark68Net.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceRecognitionNet.loadFromUri('/TSUISLARS/faceApi')
        ]);

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
        } else if (capturedDescriptor) {
           
            statusText.textContent = "⚠️ Only captured image found. Cannot proceed with matching.";
            return;
        }

        startVideo();

        function startVideo() {
            navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } })
                .then(stream => {
                    video.srcObject = stream;
                    video.onloadeddata = () => requestAnimationFrame(detectAndMatchFace);
                })
                .catch(console.error);
        }

        let lastMismatchLoggedTime = 0;
        let matchFound = false;

        async function detectAndMatchFace() {
            if (matchFound) return;

            const detection = await faceapi.detectSingleFace(video, new faceapi.TinyFaceDetectorOptions({ inputSize: 320 }))
                .withFaceLandmarks()
                .withFaceDescriptor();

            if (!detection) {
                statusText.textContent = "No face detected";
                videoContainer.style.borderColor = "gray";
                return requestAnimationFrame(detectAndMatchFace);
            }

            const match = faceMatcher.findBestMatch(detection.descriptor);

            if (match.label === userId && match.distance < 0.35) {
                if (matchMode === "both") {
                    
                    const distToBase = faceapi.euclideanDistance(detection.descriptor, baseDescriptor);
                    const distToCaptured = faceapi.euclideanDistance(detection.descriptor, capturedDescriptor);
                    if (distToBase < 0.35 && distToCaptured < 0.35) {
                        onMatchSuccess();
                    } else {
                        onMatchFailure();
                    }
                } else if (matchMode === "baseOnly") {
                    onMatchSuccess();
                }
            } else {
                onMatchFailure();
            }

            requestAnimationFrame(detectAndMatchFace);
        }

        function onMatchSuccess() {
            statusText.textContent = `${userName}, Face matched ✅`;
            matchFound = true;
            videoContainer.style.borderColor = "green";
            setTimeout(() => {
                showSuccessAndCapture();
            }, 1000);
        }

        function onMatchFailure() {
            statusText.textContent = "Face not matched ❌";
            videoContainer.style.borderColor = "red";

            const now = Date.now();
            const cooldownMs = 5000;

            if (now - lastMismatchLoggedTime >= cooldownMs) {
                lastMismatchLoggedTime = now;

                const entryType = document.getElementById("Entry")?.value || "";
                fetch("/TSUISLARS/Geo/LogFaceMatchFailure", {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ Type: entryType })
                });
            }
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
                    .detectSingleFace(img, new faceapi.TinyFaceDetectorOptions())
                    .withFaceLandmarks()
                    .withFaceDescriptor();
                return detection?.descriptor || null;
            } catch (err) {
                console.warn(`Error loading descriptor from ${imageUrl}:`, err);
                return null;
            }
        }

        window.captureImageAndSubmit = async function (entryType) {
            if (!window.capturedDataURL) {
                alert("No capture image available");
                statusText.textContent = "Please try again — no image captured.";
                capturedImage.style.display = "none";
                video.style.display = "block";
                if (punchInButton) punchInButton.style.display = "none";
                if (punchOutButton) punchOutButton.style.display = "none";
                requestAnimationFrame(detectAndMatchFace);
                return;
            }

            EntryTypeInput.value = entryType;
            capturedImage.style.display = "block";
            video.style.display = "none";

            Swal.fire({
                title: "Please wait...",
                allowOutsideClick: false,
                showConfirmButton: false,
                didOpen: () => Swal.showLoading()
            });

            fetch("/TSUISLARS/Geo/AttendanceData", {
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
                        Swal.fire("Face Recognized, But Error!", "Server rejected attendance.", "error")
                        .then(() => location.reload());
                    }
                })
                .catch(() => {
                    Swal.fire("Error!", "Submission failed.", "error");
                });
        };
    });
</script>
