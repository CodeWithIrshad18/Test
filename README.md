const entryType = document.getElementById("Entry").value; // Punch In or Punch Out
let lastFailureTime = 0;

function logFailure() {
    const now = Date.now();
    if (now - lastFailureTime < 3000) return; // 3 sec cooldown
    lastFailureTime = now;

    fetch("/AS/Geo/LogFaceMatchFailure", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ Type: entryType })
    }).catch(err => console.error("Error logging failure:", err));
}

async function detectAndMatchFace() {
    if (matchFound) return;

    const detections = await faceapi
        .detectAllFaces(video, new faceapi.TinyFaceDetectorOptions({ inputSize: 320 }))
        .withFaceLandmarks()
        .withFaceDescriptors();

    if (detections.length === 0) {
        // No face → no logging
        statusText.textContent = "No face detected";
        videoContainer.style.borderColor = "gray";
        return requestAnimationFrame(detectAndMatchFace);
    }

    if (detections.length > 1) {
        // Multiple faces → no logging
        statusText.textContent = "❌ Multiple faces detected. Please ensure only one face is visible.";
        videoContainer.style.borderColor = "red";
        return requestAnimationFrame(detectAndMatchFace);
    }

    // Single face detected
    const detection = detections[0];
    const match = faceMatcher.findBestMatch(detection.descriptor);

    if (match.label === userId && match.distance < 0.35) {
        // Possible match → check stricter conditions if both images exist
        if (matchMode === "both") {
            const distToBase = faceapi.euclideanDistance(detection.descriptor, baseDescriptor);
            const distToCaptured = faceapi.euclideanDistance(detection.descriptor, capturedDescriptor);

            if (distToBase < 0.35 && distToCaptured < 0.35) {
                onMatchSuccess();
            } else {
                statusText.textContent = "❌ Face does not match with uploaded images.";
                videoContainer.style.borderColor = "red";
                logFailure(); // only here for mismatch
            }
        } else {
            onMatchSuccess();
        }
    } else {
        // Face detected but does not match
        statusText.textContent = "❌ Face does not match with reference images.";
        videoContainer.style.borderColor = "red";
        logFailure(); // log here
    }

    requestAnimationFrame(detectAndMatchFace);
}




these are my two buttons 
this is my two buttons
 
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
