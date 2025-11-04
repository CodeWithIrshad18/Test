nothing happens no dropdown option is selected based on the PeriodTransactionId, see my full code

   <a href="#"
   class="refNoLink"
   data-KPIID="@item.KPIID"
   data-ID="@item.ID"
   data-KPICode="@item.KPICode"
   data-PeriodTransactionID="@item.PeriodTransactionID"
   data-Hold="@item.Hold"
   data-HoldReason="@item.HoldReason"
   data-KPIID="@item.KPIID"
   data-TSID="@item.TSID"
    data-Company="@item.Company"
   data-Division="@item.Division"
   data-Department="@item.Department"
   data-Section="@item.Section"
   data-UnitCode="@item.UnitCode"
   data-KPIDetails="@item.KPIDetails"
   data-PeriodicityID="@item.PeriodicityID" 
   data-FinYear="@item.FinYear" 
   data-FinYearID="@item.FinYearID" 
   data-PeriodicityName="@item.PeriodicityName">
   @item.KPIDetails
</a>

js:
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

      
const existingPeriodTransactionId = this.dataset.periodtransactionid;

if (tsid) {
    try {
        const response = await fetch(`/TPR/GetTargets?TSID=${tsid}`);
        const data = await response.json();

        if (data.length === 0) {
            console.warn("No target data found for this TSID:", tsid);
            return;
        }

        data.forEach(item => {
            const opt = document.createElement("option");
            opt.value = item.ID;
            opt.textContent = item.PeriodicityTransactionID;
            periodSelect.appendChild(opt);
        });

        periodSelect.dataset.periodData = JSON.stringify(data);

        if (existingPeriodTransactionId) {
            const match = data.find(p => p.PeriodicityTransactionID === existingPeriodTransactionId);
            if (match) {
                periodSelect.value = match.ID;
                targetInput.value = match.TargetValue || "";
                document.getElementById("PeriodID").value = match.ID;
            } else {
                periodSelect.value = "";
                targetInput.value = "";
            }
        } else {
            periodSelect.value = "";
            targetInput.value = "";
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

view:


<div class="row g-3">
<div class="col-md-1">
  <label for="Period" class="control-label">Period</label>
</div>
<div class="col-md-7">
    <input type="hidden" asp-for="PeriodTransactionID" id="PeriodID" name="PeriodTransactionID">
  <select class="form-control form-control-sm custom-select" id="Period">
  </select>
</div>

<div class="col-md-3">
  <label for="Target" class="control-label">Target</label>
</div>
<div class="col-md-1">
  <input type="text" class="form-control form-control-sm" id="Target" autocomplete="off" readonly>
</div>
