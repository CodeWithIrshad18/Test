<script>
const statusText = document.getElementById("statusText");
let locationCheckInterval = null;

async function OnOff() {
    const punchIn = document.getElementById('PunchIn');
    const punchOut = document.getElementById('PunchOut');

    disableButtons(punchIn, punchOut);
    statusText.textContent = "📍 Fetching your location... please wait";

    try {
        const position = await getAccuratePosition({
            enableHighAccuracy: true,
            timeout: 15000,
            maximumAge: 0 // <— ensures fresh GPS reading (not cached)
        });

        const lat = roundTo(position.coords.latitude, 6);
        const lon = roundTo(position.coords.longitude, 6);

        console.log(`✅ Location fetched: ${lat}, ${lon} (accuracy: ${position.coords.accuracy}m)`);

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
            enableButtons(punchIn, punchOut);
            statusText.textContent = "✅ You are in the allowed location.";

            // Start face recognition after accurate location
            await startFaceRecognition();
        } else {
            disableButtons(punchIn, punchOut);
            statusText.textContent = `⚠️ Out of range. (~${Math.round(minDistance)}m away)`;
            Swal.fire({
                icon: "error",
                title: "Out of Range",
                text: `You are ${Math.round(minDistance)} meters away from the allowed location!`,
                allowOutsideClick: false
            });
        }

    } catch (error) {
        console.error("❌ Location error:", error);
        disableButtons(punchIn, punchOut);
        let msg = "Please check your location permission or enable location services.";
        if (error.code === 1) msg = "Permission denied. Please allow location access.";
        if (error.code === 2) msg = "Location unavailable. Please move to an open area.";
        if (error.code === 3) msg = "Location request timed out. Try again.";

        Swal.fire({
            icon: "error",
            title: "Error Fetching Location!",
            text: msg,
            confirmButtonText: "OK"
        });

        statusText.textContent = "Please refresh to try again.";
    }
}

function getAccuratePosition(options) {
    return new Promise((resolve, reject) => {
        let bestPosition = null;
        let watchId = null;
        let timeoutReached = false;

        const stopWatching = () => {
            if (watchId !== null) {
                navigator.geolocation.clearWatch(watchId);
                watchId = null;
            }
        };

        // Start watching for position improvements
        watchId = navigator.geolocation.watchPosition(
            (pos) => {
                // Save the best position (smallest accuracy)
                if (!bestPosition || pos.coords.accuracy < bestPosition.coords.accuracy) {
                    bestPosition = pos;
                }

                // If accuracy is good enough (<25m), stop early
                if (pos.coords.accuracy <= 25) {
                    stopWatching();
                    resolve(pos);
                }
            },
            (err) => {
                stopWatching();
                reject(err);
            },
            options
        );

        // Stop after timeout (15s max)
        setTimeout(() => {
            timeoutReached = true;
            stopWatching();
            if (bestPosition) {
                resolve(bestPosition); // return best we got
            } else {
                reject({ code: 3, message: "Timeout reached, no accurate fix." });
            }
        }, options.timeout || 15000);
    });
}

function calculateDistance(lat1, lon1, lat2, lon2) {
    const R = 6371000; // meters
    const toRad = angle => (angle * Math.PI) / 180;
    const dLat = toRad(lat2 - lat1);
    const dLon = toRad(lon2 - lon1);

    const a =
        Math.sin(dLat / 2) ** 2 +
        Math.cos(toRad(lat1)) * Math.cos(toRad(lat2)) *
        Math.sin(dLon / 2) ** 2;

    return R * (2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a)));
}

function roundTo(num, places) {
    return +(Math.round(num + "e" + places) + "e-" + places);
}

function disableButtons(punchIn, punchOut) {
    if (punchIn) {
        punchIn.disabled = true;
        punchIn.classList.add("disabled");
    }
    if (punchOut) {
        punchOut.disabled = true;
        punchOut.classList.add("disabled");
    }
}

function enableButtons(punchIn, punchOut) {
    if (punchIn) {
        punchIn.disabled = false;
        punchIn.classList.remove("disabled");
    }
    if (punchOut) {
        punchOut.disabled = false;
        punchOut.classList.remove("disabled");
    }
}

window.onload = () => {
    OnOff(); // run location check on page load
    if (locationCheckInterval) clearInterval(locationCheckInterval);
    // recheck every 5 minutes
    locationCheckInterval = setInterval(OnOff, 300000);
};
</script>





<script>
     const statusText = document.getElementById("statusText");
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

                 statusText.textContent = "Please refresh if your are in location...";
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

            statusText.textContent = "Please refresh if your are in location...";
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

    window.onload = () => {
        OnOff(); 
        if (locationCheckInterval) clearInterval(locationCheckInterval);
        
        locationCheckInterval = setInterval(OnOff, 300000);
    };
</script>
