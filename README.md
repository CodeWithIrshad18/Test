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
    <div class="col-md-1 deactive-field" style="display: none;">
        <label for="HoldReason" class="control-label">Hold Reason</label>
    </div>

    <div class="col-md-2 deactive-field" style="display: none;">
        <input asp-for="HoldReason" class="form-control form-control-sm" id="HoldReason" name="HoldReason">
    </div>
</div>


data-hold="True" data-holdreason="test"


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
            document.getElementById("HoldReason").value = this.dataset.holdreason;
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
