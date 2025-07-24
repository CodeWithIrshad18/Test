i have this code please make changes to this 
        const safeUserName = userName.replace(/\s+/g, "%20");

        const timestamp = Date.now(); 

const descriptors = [
    await loadDescriptor(`/AS/Images/${userId}-Captured.jpg?t=${timestamp}`),
    await loadDescriptor(`/AS/Images/${userId}-${safeUserName}.jpg?t=${timestamp}`)
].filter(d => d !== null);


       
        if (descriptors.length === 0) {
            statusText.textContent = "❌ No reference image(s) found.Please Upload your Image";
            return; 
        }

        const faceMatcher = new faceapi.FaceMatcher(
            [new faceapi.LabeledFaceDescriptors(userId, descriptors)],
            0.35
        );
