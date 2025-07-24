const safeUserName = userName.replace(/\s+/g, "%20");
const timestamp = Date.now(); 

// Image paths to try (order matters)
const imageUrls = [
    `/AS/Images/${userId}-Captured.jpg?t=${timestamp}`,
    `/AS/Images/${userId}-${safeUserName}.jpg?t=${timestamp}`
];

const descriptors = [];

for (const url of imageUrls) {
    const descriptor = await loadDescriptor(url);
    if (descriptor) {
        descriptors.push(descriptor);
    } else {
        console.warn(`Image not found or no face detected: ${url}`);
    }
}

if (descriptors.length === 0) {
    statusText.textContent = "❌ No reference image(s) found. Please upload your image.";
    return;
}

// Create face matcher only if at least one descriptor is valid
const faceMatcher = new faceapi.FaceMatcher(
    [new faceapi.LabeledFaceDescriptors(userId, descriptors)],
    0.35
);

async function loadDescriptor(imageUrl) {
    try {
        const img = await faceapi.fetchImage(imageUrl);
        const detection = await faceapi.detectSingleFace(img).withFaceLandmarks().withFaceDescriptor();
        return detection?.descriptor || null;
    } catch (e) {
        return null;
    }
}





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
