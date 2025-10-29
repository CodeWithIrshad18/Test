        [HttpGet]
        public async Task<JsonResult> GetTargets(Guid TSID)
        {
            using (var connection = new SqlConnection(GetSAPConnectionString()))
            {
                string query = @"
            select pm.ID,tj.PeriodicityTransactionID,tj.TargetValue from App_KPIMaster_NOPR ts 
            inner join App_TargetSetting_NOPR td
            on ts.ID =td.KPIID
            inner join App_TargetSettingDetails_NOPR tj
            on td.ID = tj.MasterID 
 inner join App_PeriodicityTransaction pm
             on pm.PeriodicityName = tj.PeriodicityTransactionID
where td.ID = @TSID order by pm.Sl_no";

                var result = await connection.QueryAsync(query, new { TSID = TSID });
                return Json(result);
            }
        }


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
        document.getElementById("KPIID").value = this.dataset.kpiid;
        document.getElementById("PeriodicityID").value = this.dataset.periodicityname;

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
                    opt.value = item.PeriodicityTransactionID;
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
    const selectedPeriod = this.value;
    const periodData = this.dataset.periodData ? JSON.parse(this.dataset.periodData) : [];
    const selectedItem = periodData.find(p => p.PeriodicityTransactionID === selectedPeriod);
    targetInput.value = selectedItem ? selectedItem.TargetValue : "";
});
