this is my js 

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

    const holdSection = document.getElementById("HoldSection"); 
    const valueSection = document.getElementById("ValueSection");
    const ytdValueSection = document.getElementById("YTDValueSection");
    const YTDValueSectionlabel = document.getElementById("YTDValueSectionlabel");
    const ValueSectionlabel = document.getElementById("ValueSectionlabel");


    function show(el) {
        el.classList.remove("hidden");
        el.classList.add("visible");
    }
    function hide(el) {
        el.classList.remove("visible");
        el.classList.add("hidden");
    }


    function toggleHoldFields() {
        if (holdYes.checked) {
            holdReasonField.forEach(el => show(el));
            

            hide(valueSection);
            hide(ytdValueSection);
            hide(YTDValueSectionlabel);
            hide(ValueSectionlabel);
        } else {
            holdReasonField.forEach(el => hide(el));
          
            holdReasonInput.value = "";

            show(valueSection);
            show(ytdValueSection);
                 show(YTDValueSectionlabel);
            show(ValueSectionlabel);
        }
    }

    holdYes.addEventListener("change", toggleHoldFields);
    holdNo.addEventListener("change", toggleHoldFields);


    hide(holdSection);
    hide(valueSection);
    hide(ytdValueSection);
         hide(YTDValueSectionlabel);
            hide(ValueSectionlabel);
    holdReasonField.forEach(el => hide(el));

    refNoLinks.forEach(link => {
        link.addEventListener("click", async function (event) {
            event.preventDefault();
            KPIMaster.style.display = "block";


            holdNo.checked = true;
            holdReasonInput.value = "";
            hide(holdSection);
            hide(valueSection);
            hide(ytdValueSection);
             hide(YTDValueSectionlabel);
            hide(ValueSectionlabel);
            holdReasonField.forEach(el => hide(el));

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
    periodSelect.addEventListener("change", function () {
        const selectedID = this.value;
        const periodData = this.dataset.periodData ? JSON.parse(this.dataset.periodData) : [];

        if (!selectedID) {
            holdYes.checked = false;
            holdNo.checked = true;
            holdReasonInput.value = "";
            holdReasonField.forEach(el => hide(el));
            hide(holdSection);
            hide(valueSection);

            hide(ytdValueSection);
             hide(YTDValueSectionlabel);
            hide(ValueSectionlabel);
            return;
        }


        show(holdSection);
        show(valueSection);
        show(ytdValueSection);
         show(YTDValueSectionlabel);
            show(ValueSectionlabel);

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



<div class="col-md-3 offset-md-8">
     <div id="ValueSectionlabel" class="fade-section hidden">
  <label for="Value" class="control-label">Value</label>
</div>
</div>
<div class="col-md-1">
    <div id="ValueSection" class="fade-section hidden">
  <input asp-for="Value" type="text" class="form-control form-control-sm" id="Value" autocomplete="off">
</div>
</div>

<div class="col-md-3 offset-md-8">
    <div id="YTDValueSectionlabel" class="fade-section hidden">
  <label for="YTDValue" class="control-label">YTD Value</label>
</div>
</div>
<div class="col-md-1">
    <div id="YTDValueSection" class="fade-section hidden">
  <input asp-for="YTDValue" type="text" class="form-control form-control-sm" id="YTDValue" autocomplete="off">
</div>



       [HttpGet]
       public async Task<JsonResult> GetTargets(Guid TSID)
       {
           using (var connection = new SqlConnection(GetSAPConnectionString()))
           {
               string query = @"
           SELECT 
               pm.ID,
               tj.PeriodicityTransactionID,
               tj.TargetValue,
               t2.Hold,
               t2.HoldReason
           FROM App_KPIMaster_NOPR ts
           INNER JOIN App_TargetSetting_NOPR td
               ON ts.ID = td.KPIID
           INNER JOIN App_TargetSettingDetails_NOPR tj
               ON td.ID = tj.MasterID
           INNER JOIN App_PeriodicityTransaction pm
               ON pm.PeriodicityName = tj.PeriodicityTransactionID
           OUTER APPLY (
               SELECT TOP 1 Hold, HoldReason
               FROM App_KPIDetails_NOPR t
               WHERE t.KPIID = ts.ID 
                 AND t.PeriodTransactionID = pm.ID
               ORDER BY t.CreatedOn DESC
           ) t2
           WHERE td.ID = @TSID
           ORDER BY pm.Sl_no;
       ";

               var result = await connection.QueryAsync(query, new { TSID = TSID });
               return Json(result);
           }
       }


ID	KPIID	PeriodTransactionID	Value	FinYearID	CreatedBy	CreatedOn	KPIDate	YTDValue	KPITime	Hold	HoldReason
B996076B-73AD-4936-BBFD-A21B37CDD2C5	63A0D5E5-F8B6-45B1-8720-92DD5BCBA069	C67B88E5-F524-49A6-8252-5D17705A621F	70.0000	858C5BF6-2548-4BBE-A7E1-20A159910260	151514	2025-11-06 12:15:25.643	NULL	80.0000	NULL	0	NULL
EFEA63DB-A1FE-409B-BBB0-A9EFB6319C37	63A0D5E5-F8B6-45B1-8720-92DD5BCBA069	80F673AC-F609-4521-BA77-858807B07628	NULL	858C5BF6-2548-4BBE-A7E1-20A159910260	151514	2025-11-06 12:15:07.057	NULL	NULL	NULL	1	Projects are on Hold
