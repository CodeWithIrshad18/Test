protected void btnsave_Click(object sender, EventArgs e)
{
    string vc = Session["UserName"].ToString();
    string sYear = Year.SelectedValue;
    DataSet ds = (DataSet)ViewState["App_Bonus_Generation_Details"];

    for (int i = 0; i < PageRecordDataSet.Tables["Table1"].Rows.Count; i++)
    {
        DataRow newRow = PageRecordDataSet.Tables["App_Bonus_Generation_Details"].NewRow();

        foreach (DataColumn col in PageRecordDataSet.Tables["App_Bonus_Generation_Details"].Columns)
        {
            var val = PageRecordDataSet.Tables["Table1"].Rows[i][col.ColumnName];

            // Safe assignment
            if (val == DBNull.Value || string.IsNullOrWhiteSpace(val.ToString()))
                newRow[col.ColumnName] = 0;   // default
            else
                newRow[col.ColumnName] = val;
        }

        PageRecordDataSet.Tables["App_Bonus_Generation_Details"].Rows.Add(newRow);
    }
}



<script>
    let locationCheckInterval = null;

    async function OnOff() {
        const punchIn = document.getElementById('PunchIn');
        const punchOut = document.getElementById('PunchOut');

        // Disable both by default
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
            } else {
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
                    text: `You are ${Math.round(minDistance)} meters away from the allowed location!`
                });
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
        }
    }

    function getCurrentPosition(options) {
        return new Promise((resolve, reject) => {
            navigator.geolocation.getCurrentPosition(resolve, reject, options);
        });
    }

    function calculateDistance(lat1, lon1, lat2, lon2) {
        const R = 6371000; // meters
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
        OnOff(); // Initial check
        if (locationCheckInterval) clearInterval(locationCheckInterval);
        // Re-check every 60 seconds
        locationCheckInterval = setInterval(OnOff, 60000);
    };
</script>




<script>
    let locationCheckInterval = null;

    async function OnOff() {
        const punchIn = document.getElementById('PunchIn');
        const punchOut = document.getElementById('PunchOut');

        if (punchIn) punchIn.disabled = true;
        if (punchOut) punchOut.disabled = true;

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
                if (punchIn) punchIn.disabled = false;
                if (punchOut) punchOut.disabled = false;
            } else {
                if (punchIn) punchIn.disabled = true;
                if (punchOut) punchOut.disabled = true;

                Swal.fire({
                    icon: "error",
                    title: "Out of Range",
                    text: `You are ${Math.round(minDistance)} meters away from the allowed location!`
                });
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

            if (punchIn) punchIn.disabled = true;
            if (punchOut) punchOut.disabled = true;
        }
    }

    // Wrap geolocation in a Promise
    function getCurrentPosition(options) {
        return new Promise((resolve, reject) => {
            navigator.geolocation.getCurrentPosition(resolve, reject, options);
        });
    }

    function calculateDistance(lat1, lon1, lat2, lon2) {
        const R = 6371000; // meters
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

    // Run when page loads
    window.onload = () => {
        OnOff(); // Initial check
        if (locationCheckInterval) clearInterval(locationCheckInterval);
        // 🔁 Re-check every 60 seconds
        locationCheckInterval = setInterval(OnOff, 60000);
    };
</script>




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
