i have this three buttons is this code right to hide these 

 <button type="button" id="captureBtn" class="btn btn-primary">Capture</button>
 <button type="button" id="retakeBtn" class="btn btn-danger" style="display: none;">Retake</button>
<button type="submit" class="btn btn-success" id="submitBtn" disabled>Save Details</button>

and this is my script 
<script>
    const uploadLimitReached = @ViewBag.UploadLimitReached.ToString().ToLower(); 
    const isAlreadyRegistered = @ViewBag.IsAlreadyRegistered.ToString().ToLower(); 

    if (uploadLimitReached === "true" && isAlreadyRegistered === "false") {
       
        const uploadButton = document.getElementById("submitBtn");
        const captureBtn = document.getElementById("captureBtn");
        const retakeButton = document.getElementById("retakeBtn");
        const video = document.getElementById("video");

        if (uploadButton) uploadButton.style.display = "none";
        if (captureBtn) captureBtn.style.display = "none";
        if (retakeButton) retakeButton.style.display = "none";
        if (video && video.srcObject) {
            const stream = video.srcObject;
            const tracks = stream.getTracks();
            tracks.forEach(track => track.stop());
            video.srcObject = null;
        }

        
        document.getElementById("statusText").textContent = "Upload limit of 20 users reached. Only existing users can update.";
    }
</script>
