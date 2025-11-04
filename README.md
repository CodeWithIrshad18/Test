// ✅ Get existing PeriodTransactionId from dataset
const existingPeriodTransactionId = this.dataset.periodtransactionid;

// after your fetch successfully populates dropdown options
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

        // ✅ Pre-select existing PeriodTransactionId if present
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


                    periodSelect.value = "";
                    targetInput.value = "";
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

this is my query 

SELECT 
    KI.ID AS KPIID,
    KD.ID,
    KD.Value,
 KD.Hold,
    KD.HoldReason,
KD.PeriodTransactionID,
    KI.Company,
    TR.FinYearID,
    KI.Division,
    TR.ID AS TSID,
    KI.Department,
    KI.Section,
    pm.PeriodicityName,
    KI.KPIDetails,
    KI.UnitID,
    SF.FinYear,
    KI.CreatedBy,
    KI.KPICode,
    KI.PeriodicityID,
    TR.BaseLine,
    TR.Target,
    TR.BenchMarkPatner,
    TR.BenchMarkValue,
    UM.UnitCode,
    KI.NoofDecimal
FROM App_KPIMaster_NOPR KI
LEFT JOIN App_UOM_NOPR UM ON KI.UnitID = UM.ID
LEFT JOIN App_PeriodicityMaster_NOPR pm ON KI.PeriodicityID = pm.ID
LEFT JOIN (
    SELECT 
        k1.*
    FROM App_KPIDetails_NOPR k1
    INNER JOIN (
        SELECT KPIID, MAX(CreatedOn) AS MaxCreatedOn
        FROM App_KPIDetails_NOPR
        GROUP BY KPIID
    ) k2 ON k1.KPIID = k2.KPIID AND k1.CreatedOn = k2.MaxCreatedOn
) KD ON KD.KPIID = KI.ID
LEFT JOIN App_TargetSetting_NOPR TR ON TR.KPIID = KI.ID
LEFT JOIN App_Sys_FinYear SF ON TR.FinYearID = SF.ID
WHERE
    KI.KPISPOC = @UserId 
    AND (KI.Deactivate IS NULL OR KI.Deactivate = 0)
    AND (@search IS NULL OR KI.KPICode LIKE '%' + @search + '%' OR UM.UnitCode LIKE '%' + @search + '%')
    AND (@search2 IS NULL OR KI.Department LIKE '%' + @search2 + '%')
    AND (@search3 IS NULL OR KI.KPIDetails LIKE '%' + @search3 + '%')
ORDER BY KI.KPIDetails
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY

and this is my dropdown 

<div class="col-md-1">
  <label for="Period" class="control-label">Period</label>
</div>
<div class="col-md-7">

    <input type="hidden" asp-for="PeriodTransactionID" id="PeriodID" name="PeriodTransactionID">
  <select class="form-control form-control-sm custom-select" id="Period">
  </select>
</div>

and this is my data-bs for PeriodTransactionId
periodtransactionid="87b70b1d-e095-4ca4-a3a8-980ea72a71f7"


i want that when for existing check for which PeriodTransactionId along with data-id="da956136-996f-40da-8098-2a0a5042aa86" is hold then select the value in Dropdown according to that
