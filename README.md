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

    // 🔹 Show/Hide HoldReason based on Hold selection
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

    // 🔹 Handle click on refNoLink
    refNoLinks.forEach(link => {
        link.addEventListener("click", async function (event) {
            event.preventDefault();
            KPIMaster.style.display = "block";

            // 🟢 Fill static info
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

            // 🟢 Handle Hold field
            const isHold = this.dataset.hold === "True" || this.dataset.hold === "true";
            if (isHold) holdYes.checked = true; else holdNo.checked = true;
            document.getElementById("HoldReason").value = this.dataset.holdreason || "";
            toggleHoldFields();  

            const tsid = this.dataset.tsid;
            const existingPeriodTransactionId = this.dataset.periodtransactionid?.trim();

            // reset dropdown and target
            periodSelect.innerHTML = '<option value="">Select</option>';
            periodSelect.dataset.periodData = "[]";
            targetInput.value = "";

            if (!tsid) return;

            try {
                const response = await fetch(`/TPR/GetTargets?TSID=${tsid}`);
                const data = await response.json();

                if (!Array.isArray(data) || data.length === 0) {
                    console.warn("No target data found for this TSID:", tsid);
                    return;
                }

                // 🟢 Populate dropdown
                data.forEach(item => {
                    const opt = document.createElement("option");
                    opt.value = item.ID;
                    opt.textContent = item.PeriodTransactionID || item.PeriodicityTransactionID || "(No Name)";
                    periodSelect.appendChild(opt);
                });

                periodSelect.dataset.periodData = JSON.stringify(data);

                // 🟢 Find matching or first (latest) record
                let selectedPeriod = null;
                if (existingPeriodTransactionId) {
                    selectedPeriod = data.find(p => 
                        p.PeriodTransactionID === existingPeriodTransactionId ||
                        p.ID === existingPeriodTransactionId
                    );
                }
                if (!selectedPeriod) selectedPeriod = data[0]; // fallback to first (latest)

                // 🟢 Populate fields based on selected record
                if (selectedPeriod) {
                    periodSelect.value = selectedPeriod.ID;
                    targetInput.value = selectedPeriod.TargetValue || "";
                    document.getElementById("PeriodID").value = selectedPeriod.ID;

                    // update Hold fields
                    if (selectedPeriod.Hold === true || selectedPeriod.Hold === "True" || selectedPeriod.Hold === 1) {
                        holdYes.checked = true;
                        holdReasonInput.value = selectedPeriod.HoldReason || "";
                    } else {
                        holdNo.checked = true;
                        holdReasonInput.value = "";
                    }
                    toggleHoldFields();
                }

            } catch (error) {
                console.error("Error fetching target details:", error);
            }

            // Hide delete button
            if (deleteButton) deleteButton.style.display = "none";

            // Save or delete button handlers
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

    // 🔹 Handle Period dropdown change
    periodSelect.addEventListener("change", function () {
        const selectedID = this.value;
        const periodData = this.dataset.periodData ? JSON.parse(this.dataset.periodData) : [];
        const selectedItem = periodData.find(p => p.ID === selectedID);

        if (!selectedItem) return;

        targetInput.value = selectedItem.TargetValue || '';
        document.getElementById("PeriodID").value = selectedItem.ID;

        // 🟢 Update Hold and HoldReason based on dropdown change
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





this is my full js. please make changes according to this 

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
            document.getElementById("HoldReason").value = this.dataset.holdreason || "";
            document.getElementById("KPIID").value = this.dataset.kpiid;
            document.getElementById("PeriodicityID").value = this.dataset.periodicityname;


            const isHold = this.dataset.hold === "True" || this.dataset.hold === "true";
        if (isHold) {
            holdYes.checked = true;
        } else {
            holdNo.checked = true;
        }
        toggleHoldFields(); 
  

            const tsid = this.dataset.tsid;


            periodSelect.innerHTML = '<option value="">Select</option>';
            periodSelect.dataset.periodData = "[]";
            targetInput.value = "";

      
const existingPeriodTransactionId = this.dataset.periodtransactionid?.trim();

if (tsid) {
    try {
        const response = await fetch(`/TPR/GetTargets?TSID=${tsid}`);
        const data = await response.json();

        if (!Array.isArray(data) || data.length === 0) {
            console.warn("No target data found for this TSID:", tsid);
            return;
        }

        periodSelect.innerHTML = '<option value="">Select</option>';
        data.forEach(item => {
            const opt = document.createElement("option");
            opt.value = item.ID;
            opt.textContent = item.PeriodTransactionID || item.PeriodicityTransactionID || "(No Name)";
            periodSelect.appendChild(opt);
        });

        periodSelect.dataset.periodData = JSON.stringify(data);

        console.log("Existing Period:", existingPeriodTransactionId, "Available:", data.map(d => d.ID || d.ID));

        if (existingPeriodTransactionId) {
            const match = data.find(p =>
                p.ID === existingPeriodTransactionId ||
                p.ID === existingPeriodTransactionId
            );

            if (match) {
                periodSelect.value = match.ID;
                targetInput.value = match.TargetValue || "";
                document.getElementById("PeriodID").value = match.ID;
            } else {
                console.warn("No matching period found for ID:", existingPeriodTransactionId);
            }
        }

    } catch (error) {
        console.error("Error fetching target details:", error);
    }
}

            if (submitButton) {
                submitButton.addEventListener("click", function () {
                    actionTypeInput.value = "save";
                });
            }
            if (deleteButton) deleteButton.style.display = "none";

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
    const selectedItem = periodData.find(p => p.ID === selectedID);


    targetInput.value = selectedItem ? selectedItem.TargetValue : '';

    document.getElementById("PeriodID").value = selectedID;
});

});
</script>
