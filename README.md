<style>
.fade-section {
    transition: opacity 0.3s ease, max-height 0.3s ease;
    overflow: hidden;
}
.hidden {
    opacity: 0;
    max-height: 0;
    pointer-events: none;
}
.visible {
    opacity: 1;
    max-height: 500px;
}
</style>

<script>
document.addEventListener('DOMContentLoaded', function () {
    const KPIMaster = document.getElementById("form");
    const refNoLinks = document.querySelectorAll(".refNoLink");
    const deleteButton = document.getElementById("deleteButton");
    const submitButton = document.getElementById("submitButton");
    const actionTypeInput = document.getElementById("actionType");
    const periodSelect = document.getElementById("Period");
    const targetInput = document.getElementById("Target");

    const holdYes = document.getElementById("YES");
    const holdNo = document.getElementById("NO");
    const holdReasonField = document.querySelectorAll(".deactive-field");
    const holdReasonInput = document.getElementById("HoldReason");

    // Sections to show/hide
    const holdSection = document.getElementById("HoldSection"); // wrap your hold radio & reason divs inside a parent div with id="HoldSection"
    const valueSection = document.getElementById("ValueSection"); // wrap Value field div in <div id="ValueSection">
    const ytdValueSection = document.getElementById("YTDValueSection"); // wrap YTD field div in <div id="YTDValueSection">

    // Helper fade functions
    function show(el) {
        el.classList.remove("hidden");
        el.classList.add("visible");
    }
    function hide(el) {
        el.classList.remove("visible");
        el.classList.add("hidden");
    }

    // toggle hold reason visibility
    function toggleHoldFields() {
        if (holdYes.checked) {
            holdReasonField.forEach(el => show(el));
            holdReasonInput.setAttribute("required", "required");
            // hide Value & YTDValue when Hold = YES
            hide(valueSection);
            hide(ytdValueSection);
        } else {
            holdReasonField.forEach(el => hide(el));
            holdReasonInput.removeAttribute("required");
            holdReasonInput.value = "";
            // show Value & YTDValue when Hold = NO
            show(valueSection);
            show(ytdValueSection);
        }
    }

    holdYes.addEventListener("change", toggleHoldFields);
    holdNo.addEventListener("change", toggleHoldFields);

    // Initially hide everything
    hide(holdSection);
    hide(valueSection);
    hide(ytdValueSection);
    holdReasonField.forEach(el => hide(el));

    refNoLinks.forEach(link => {
        link.addEventListener("click", async function (event) {
            event.preventDefault();
            KPIMaster.style.display = "block";

            // reset hold + fields
            holdNo.checked = true;
            holdReasonInput.value = "";
            hide(holdSection);
            hide(valueSection);
            hide(ytdValueSection);
            holdReasonField.forEach(el => hide(el));

            // Fill form fields
            document.getElementById("KPICode").value = this.dataset.kpicode;
            document.getElementById("Company").value = this.dataset.company;
            document.getElementById("Department").value = this.dataset.department;
            document.getElementById("Division").value = this.dataset.division;
            document.getElementById("Section").value = this.dataset.section;
            document.getElementById("UnitCode").value = this.dataset.unitcode;
            document.getElementById("KPIDefination").value = this.dataset.kpidetails;
            document.getElementById("FinYear").value = this.dataset.finyear;
            document.getElementById("FinYearID").value = this.dataset.finyearid;
            document.getElementById("KPIID").value = this.dataset.kpiid;
            document.getElementById("PeriodicityID").value = this.dataset.periodicityname;

            const tsid = this.dataset.tsid;
            periodSelect.innerHTML = '<option value="">Select</option>';
            periodSelect.dataset.periodData = "[]";
            targetInput.value = "";
            document.getElementById("PeriodID").value = "";

            if (!tsid) return;

            try {
                const response = await fetch(`/TPR/GetTargets?TSID=${tsid}`);
                const data = await response.json();

                if (!Array.isArray(data) || data.length === 0) {
                    console.warn("No target data found for TSID:", tsid);
                    return;
                }

                data.forEach(item => {
                    const opt = document.createElement("option");
                    opt.value = item.ID;
                    opt.textContent = item.PeriodTransactionID || item.PeriodicityTransactionID || "(Unnamed Period)";
                    periodSelect.appendChild(opt);
                });

                periodSelect.dataset.periodData = JSON.stringify(data);

            } catch (error) {
                console.error("Error fetching target details:", error);
            }

            if (deleteButton) deleteButton.style.display = "none";

            if (submitButton) {
                submitButton.addEventListener("click", function () {
                    actionTypeInput.value = "save";
                });
            }

            if (deleteButton) {
                deleteButton.addEventListener("click", function () {
                    Swal.fire({
                        title: 'Are you sure?',
                        text: "Do you really want to delete this Unit?",
                        icon: 'warning',
                        showCancelButton: true,
                        confirmButtonColor: '#3085d6',
                        cancelButtonColor: '#d33',
                        confirmButtonText: 'Yes, delete it!',
                        cancelButtonText: 'Cancel'
                    }).then((result) => {
                        if (result.isConfirmed) {
                            actionTypeInput.value = "delete";
                            KPIMaster.submit();
                        }
                    });
                });
            }
        });
    });

    // Dropdown logic
    periodSelect.addEventListener("change", function () {
        const selectedID = this.value;
        const periodData = this.dataset.periodData ? JSON.parse(this.dataset.periodData) : [];

        if (!selectedID) {
            // Reset + hide all
            holdYes.checked = false;
            holdNo.checked = true;
            holdReasonInput.value = "";
            holdReasonField.forEach(el => hide(el));
            hide(holdSection);
            hide(valueSection);
            hide(ytdValueSection);
            return;
        }

        // Valid selection → show Hold + Value/YTDValue
        show(holdSection);
        show(valueSection);
        show(ytdValueSection);

        const selectedItem = periodData.find(p => p.ID === selectedID);
        if (!selectedItem) return;

        targetInput.value = selectedItem.TargetValue || '';
        document.getElementById("PeriodID").value = selectedItem.ID;

        if (selectedItem.Hold === true || selectedItem.Hold === "True" || selectedItem.Hold === 1) {
            holdYes.checked = true;
            holdReasonInput.value = selectedItem.HoldReason || "";
        } else {
            holdNo.checked = true;
            holdReasonInput.value = "";
        }

        toggleHoldFields();
    });
});
</script>

<div id="HoldSection"> ... hold radios + hold reason ... </div>

<div id="ValueSection" class="fade-section hidden"> ... Value input ... </div>

<div id="YTDValueSection" class="fade-section hidden"> ... YTD Value input ... </div>



<script>
document.addEventListener('DOMContentLoaded', function () {
    const KPIMaster = document.getElementById("form");
    const refNoLinks = document.querySelectorAll(".refNoLink");
    const deleteButton = document.getElementById("deleteButton");
    const submitButton = document.getElementById("submitButton");
    const actionTypeInput = document.getElementById("actionType");
    const periodSelect = document.getElementById("Period");
    const targetInput = document.getElementById("Target");

    const holdYes = document.getElementById("YES");
    const holdNo = document.getElementById("NO");
    const holdReasonField = document.querySelectorAll(".deactive-field");
    const holdReasonInput = document.getElementById("HoldReason");

    function toggleHoldFields() {
        if (holdYes.checked) {
            holdReasonField.forEach(el => el.style.display = "block");
            holdReasonInput.setAttribute("required", "required");
        } else {
            holdReasonField.forEach(el => el.style.display = "none");
            holdReasonInput.removeAttribute("required");
            holdReasonInput.value = "";
        }
    }

    holdYes.addEventListener("change", toggleHoldFields);
    holdNo.addEventListener("change", toggleHoldFields);

    refNoLinks.forEach(link => {
        link.addEventListener("click", async function (event) {
            event.preventDefault();
            KPIMaster.style.display = "block";

            document.getElementById("KPICode").value = this.dataset.kpicode;
            document.getElementById("Company").value = this.dataset.company;
            document.getElementById("Department").value = this.dataset.department;
            document.getElementById("Division").value = this.dataset.division;
            document.getElementById("Section").value = this.dataset.section;
            document.getElementById("UnitCode").value = this.dataset.unitcode;
            document.getElementById("KPIDefination").value = this.dataset.kpidetails;
            document.getElementById("FinYear").value = this.dataset.finyear;
            document.getElementById("FinYearID").value = this.dataset.finyearid;
            document.getElementById("KPIID").value = this.dataset.kpiid;
            document.getElementById("PeriodicityID").value = this.dataset.periodicityname;

            holdNo.checked = true;
            holdReasonInput.value = "";
            toggleHoldFields();

            const tsid = this.dataset.tsid;

            periodSelect.innerHTML = '<option value="">Select</option>';
            periodSelect.dataset.periodData = "[]";
            targetInput.value = "";
            document.getElementById("PeriodID").value = "";

            if (!tsid) return;

            try {
                const response = await fetch(`/TPR/GetTargets?TSID=${tsid}`);
                const data = await response.json();

                if (!Array.isArray(data) || data.length === 0) {
                    console.warn("No target data found for TSID:", tsid);
                    return;
                }

                data.forEach(item => {
                    const opt = document.createElement("option");
                    opt.value = item.ID;
                    opt.textContent = item.PeriodTransactionID || item.PeriodicityTransactionID || "(Unnamed Period)";
                    periodSelect.appendChild(opt);
                });

                periodSelect.dataset.periodData = JSON.stringify(data);

            } catch (error) {
                console.error("Error fetching target details:", error);
            }

            if (deleteButton) deleteButton.style.display = "none";

            if (submitButton) {
                submitButton.addEventListener("click", function () {
                    actionTypeInput.value = "save";
                });
            }

            if (deleteButton) {
                deleteButton.addEventListener("click", function () {
                    Swal.fire({
                        title: 'Are you sure?',
                        text: "Do you really want to delete this Unit?",
                        icon: 'warning',
                        showCancelButton: true,
                        confirmButtonColor: '#3085d6',
                        cancelButtonColor: '#d33',
                        confirmButtonText: 'Yes, delete it!',
                        cancelButtonText: 'Cancel'
                    }).then((result) => {
                        if (result.isConfirmed) {
                            actionTypeInput.value = "delete";
                            KPIMaster.submit();
                        }
                    });
                });
            }
        });
    });

   periodSelect.addEventListener("change", function () {
    const selectedID = this.value;
    const periodData = this.dataset.periodData ? JSON.parse(this.dataset.periodData) : [];

    if (!selectedID) {
        holdYes.checked = false;
        holdNo.checked = true;
        holdReasonInput.value = "";
        holdReasonField.forEach(el => el.style.display = "none");
        return;
    }

    const selectedItem = periodData.find(p => p.ID === selectedID);
    if (!selectedItem) return;

    targetInput.value = selectedItem.TargetValue || '';
    document.getElementById("PeriodID").value = selectedItem.ID;

    holdReasonField.forEach(el => el.style.display = "block");

    if (selectedItem.Hold === true || selectedItem.Hold === "True" || selectedItem.Hold === 1) {
        holdYes.checked = true;
        holdReasonInput.value = selectedItem.HoldReason || "";
    } else {
        holdNo.checked = true;
        holdReasonInput.value = "";
    }

    toggleHoldFields();
});
});
</script>
