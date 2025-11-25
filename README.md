if (isInsideRadius) {
    if (punchIn) {
        punchIn.disabled = false;
        punchIn.classList.remove("disabled");
    }
    if (punchOut) {
        punchOut.disabled = false;
        punchOut.classList.remove("disabled");
    }

    // 🔥 Prevent double call / infinite loop
    if (!window.faceRecognitionStarted) {
        window.faceRecognitionStarted = true;
        startFaceRecognition();
    }
}





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
