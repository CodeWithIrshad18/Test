this is my location logic , is there anything for better all the thing or this is good 
<script>
    function OnOff() {
        setTimeout(() => {
            var punchIn = document.getElementById('PunchIn');
            var punchOut = document.getElementById('PunchOut');


            if (punchIn) {
                punchIn.disabled = true;
                punchIn.classList.add("disabled");
            }
            if (punchOut) {
                punchOut.disabled = true;
                punchOut.classList.add("disabled");
            }

          

            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(
                    function (position) {
                       
                        const lat = roundTo(position.coords.latitude, 6);
                        const lon = roundTo(position.coords.longitude, 6);
                        

                        const locations = @Html.Raw(Json.Serialize(ViewBag.PolyData));


                        let isInsideRadius = false;
                        let minDistance = Number.MAX_VALUE;

                        locations.forEach((location) => {
                            const allowedRange = parseFloat(location.range || location.Range);
                            const distance = calculateDistance(lat, lon, location.latitude || location.Latitude, location.longitude || location.Longitude);
                            
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
                        } else {
                            Swal.fire({
                                icon: "error",
                                title: "Out of Range",
                                text: `You are ${Math.round(minDistance)} meters away from the allowed location!`
                            });
                        }
                    },
                    function (error) {
                        
                        Swal.fire({
                            title: "Error Fetching Location!",
                            text: "please check your location permission or enable location",
                            icon: "error",
                            confirmButtonText: "OK"
                        });
                    },
                    {
                        enableHighAccuracy: true,
                        timeout: 10000,
                        maximumAge: 0
                    }
                );
            } else {
                
                alert("Geolocation is not supported by this browser");
            }
        }, 500);
    }


    window.onload = OnOff;

    function calculateDistance(lat1, lon1, lat2, lon2) {
        const R = 6371000;
        const toRad = angle => (angle * Math.PI) / 180;
        let dLat = toRad(lat2 - lat1);
        let dLon = toRad(lon2 - lon1);
        let a = Math.sin(dLat / 2) * Math.sin(dLat / 2) +
            Math.cos(toRad(lat1)) * Math.cos(toRad(lat2)) *
            Math.sin(dLon / 2) * Math.sin(dLon / 2);
        let c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
        return R * c;
    }

    function roundTo(num, places) {
        return +(Math.round(num + "e" + places) + "e-" + places);
    }

    window.onload = OnOff;
</script>
