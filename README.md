.deactive-field {
    transition: all 0.3s ease;
}

.fade-section {
    transition: opacity 0.3s ease, height 0.3s ease;
    overflow: hidden;
}

<script>
document.addEventListener('DOMContentLoaded', function () {
    const KPIMaster = document.getElementById("form");
    const refNoLinks = document.querySelectorAll(".refNoLink");
    const periodSelect = document.getElementById("Period");
    const targetInput = document.getElementById("Target");
    const holdYes = document.getElementById("YES");
    const holdNo = document.getElementById("NO");
    const holdReasonField = document.querySelectorAll(".deactive-field");
    const holdReasonInput = document.getElementById("HoldReason");

    // Sections for Value and YTDValue
    const valueSection = document.getElementById("Value").closest(".col-md-1").parentElement;
    const ytdValueSection = document.getElementById("YTDValue").closest(".col-md-1").parentElement;

    // Hold Section container (the whole row that has radio buttons and reason)
    const holdSection = document.querySelector(".row.g-3.mt-1.align-items-center.mb-3");

    // Helper functions for fade in/out animation
    function fadeIn(el) {
        el.style.display = "block";
        el.style.opacity = 0;
        let opacity = 0;
        const fade = setInterval(() => {
            opacity += 0.1;
            el.style.opacity = opacity;
            if (opacity >= 1) clearInterval(fade);
        }, 25);
    }

    function fadeOut(el) {
        let opacity = 1;
        const fade = setInterval(() => {
            opacity -= 0.1;
            el.style.opacity = opacity;
            if (opacity <= 0) {
                clearInterval(fade);
                el.style.display = "none";
            }
        }, 25);
    }

    // Toggle Hold Reason inside Hold Section
    function toggleHoldFields() {
        if (holdYes.checked) {
            holdReasonField.forEach(el => fadeIn(el));
            holdReasonInput.setAttribute("required", "required");
        } else {
            holdReasonField.forEach(el => fadeOut(el));
            holdReasonInput.removeAttribute("required");
            holdReasonInput.value = "";
        }
    }

    holdYes.addEventListener("change", toggleHoldFields);
    holdNo.addEventListener("change", toggleHoldFields);

    // Initial hide
    fadeOut(holdSection);
    fadeOut(valueSection);
    fadeOut(ytdValueSection);

    refNoLinks.forEach(link => {
        link.addEventListener("click", async function (event) {
            event.preventDefault();
            KPIMaster.style.display = "block";

            // Reset states
            holdNo.checked = true;
            holdReasonInput.value = "";
            fadeOut(holdSection);
            fadeOut(valueSection);
            fadeOut(ytdValueSection);

            const tsid = this.dataset.tsid;
            periodSelect.innerHTML = '<option value="">Select</option>';
            periodSelect.dataset.periodData = "[]";
            targetInput.value = "";

            if (!tsid) return;

            try {
                const response = await fetch(`/TPR/GetTargets?TSID=${tsid}`);
                const data = await response.json();

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
        });
    });

    // When period changes
    periodSelect.addEventListener("change", function () {
        const selectedID = this.value;
        const periodData = this.dataset.periodData ? JSON.parse(this.dataset.periodData) : [];

        if (!selectedID) {
            // Hide everything again
            fadeOut(holdSection);
            fadeOut(valueSection);
            fadeOut(ytdValueSection);
            holdReasonInput.value = "";
            return;
        }

        const selectedItem = periodData.find(p => p.ID === selectedID);
        if (!selectedItem) return;

        // Show Hold section and value fields
        fadeIn(holdSection);
        fadeIn(valueSection);
        fadeIn(ytdValueSection);

        targetInput.value = selectedItem.TargetValue || '';

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






<div class="col-md-3 offset-md-8">
  <label for="Value" class="control-label">Value</label>
</div>
<div class="col-md-1">
  <input asp-for="Value" type="text" class="form-control form-control-sm" id="Value" autocomplete="off">
</div>

<div class="col-md-3 offset-md-8">
  <label for="YTDValue" class="control-label">YTD Value</label>
</div>
<div class="col-md-1">
  <input asp-for="YTDValue" type="text" class="form-control form-control-sm" id="YTDValue" autocomplete="off">
</div>



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
        <input asp-for="HoldReason" class="form-control form-control-sm" id="HoldReason" name="HoldReason" autocomplete="off">
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
