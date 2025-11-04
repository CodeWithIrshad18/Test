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

            // Fill static fields
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

            // Reset hold display (nothing selected yet)
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

                // Populate dropdown
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

            // Hide delete button
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

    // When dropdown changes → update Target + Hold logic
    periodSelect.addEventListener("change", function () {
        const selectedID = this.value;
        const periodData = this.dataset.periodData ? JSON.parse(this.dataset.periodData) : [];
        const selectedItem = periodData.find(p => p.ID === selectedID);
        if (!selectedItem) return;

        // Update target
        targetInput.value = selectedItem.TargetValue || '';
        document.getElementById("PeriodID").value = selectedItem.ID;

        // Update hold status based on selected record
        const holdValue = selectedItem.Hold ?? selectedItem.IsHold ?? selectedItem.HoldFlag;
        const reasonValue = selectedItem.HoldReason ?? selectedItem.Reason ?? "";

        if (holdValue === true || holdValue === "True" || holdValue === 1) {
            holdYes.checked = true;
            holdReasonInput.value = reasonValue;
        } else {
            holdNo.checked = true;
            holdReasonInput.value = "";
        }

        toggleHoldFields();
    });
});
</script>

 
 
 
 
 <div class="row g-3 mt-1 align-items-center mb-3">
    <div class="col-md-1">
        <label for="HOLD" class="control-label">HOLD</label>
    </div>

    <div class="col-md-1">
        <div class="form-check">
            <input type="radio" class="form-check-input" name="Hold" id="NO" value="false" autocomplete="off" checked>
            <label class="form-check-label" for="NO">NO</label>
        </div>
    </div>

    <div class="col-md-1">
        <div class="form-check">
            <input type="radio" class="form-check-input" name="Hold" id="YES" value="true" autocomplete="off">
            <label class="form-check-label" for="YES">YES</label>
        </div>
    </div>

     <div class="col-md-1">
    </div>
    <div class="col-md-1 deactive-field" style="display: ;">
        <label for="HoldReason" class="control-label">Hold Reason</label>
    </div>

    <div class="col-md-3 deactive-field" style="display: ;">
        <input asp-for="HoldReason" class="form-control form-control-sm" id="HoldReason" name="HoldReason">
    </div>
</div>


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


            const isHold = this.dataset.hold === "True" || this.dataset.hold === "true";
            if (isHold) holdYes.checked = true; else holdNo.checked = true;
            document.getElementById("HoldReason").value = this.dataset.holdreason || "";
            toggleHoldFields();  

            const tsid = this.dataset.tsid;
            const existingPeriodTransactionId = this.dataset.periodtransactionid?.trim();


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

                data.forEach(item => {
                    const opt = document.createElement("option");
                    opt.value = item.ID;
                    opt.textContent = item.PeriodTransactionID || item.PeriodicityTransactionID || "(No Name)";
                    periodSelect.appendChild(opt);
                });

                periodSelect.dataset.periodData = JSON.stringify(data);


                let selectedPeriod = null;
                if (existingPeriodTransactionId) {
                    selectedPeriod = data.find(p => 
                        p.PeriodTransactionID === existingPeriodTransactionId ||
                        p.ID === existingPeriodTransactionId
                    );
                }
                if (!selectedPeriod) selectedPeriod = data[0]; 


                if (selectedPeriod) {
                    periodSelect.value = selectedPeriod.ID;
                    targetInput.value = selectedPeriod.TargetValue || "";
                    document.getElementById("PeriodID").value = selectedPeriod.ID;


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


see the proper code and I want logic that firstly when user click on refNoLink it shows select Option of the Dropdown and no Hold reason No selected then the user Change the value from Drodpdown it automatically check the PeriodTransactionID and select according to that KPI is hold or not , but I have a concern my query sends in data-periodtransactionid a one value that is top most , how it will check for other dropdown value? 
