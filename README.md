<script>
document.addEventListener('DOMContentLoaded', function () {
    const KPIMaster = document.getElementById("form");
    const refNoLinks = document.querySelectorAll(".refNoLink");
    const deleteButton = document.getElementById("deleteButton");
    const submitButton = document.getElementById("submitButton");
    const actionTypeInput = document.getElementById("actionType");
    const periodSelect = document.getElementById("Period");
    const targetInput = document.getElementById("Target");

    refNoLinks.forEach(link => {
        link.addEventListener("click", async function (event) {
            event.preventDefault();
            KPIMaster.style.display = "block";

            // Fill form fields
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

            if (tsid) {
                try {
                    const response = await fetch(`/TPR/GetTargets?TSID=${tsid}`);
                    const data = await response.json();

                    // Populate dropdown
                    periodSelect.innerHTML = '<option value="">Select</option>';
                    data.forEach(item => {
                        const opt = document.createElement("option");
                        opt.value = item.PeriodicityTransactionID;
                        opt.textContent = item.PeriodicityTransactionID;
                        periodSelect.appendChild(opt);
                    });

                    // Save JSON for later (on change event)
                    periodSelect.dataset.periodData = JSON.stringify(data);

                    // ✅ Auto-select first period and show its target
                    if (data.length > 0) {
                        periodSelect.value = data[0].PeriodicityTransactionID;
                        targetInput.value = data[0].TargetValue;
                    } else {
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

    // ✅ Change event for dropdown — update Target textbox
    periodSelect.addEventListener("change", function () {
        const selectedPeriod = this.value;
        const periodData = this.dataset.periodData ? JSON.parse(this.dataset.periodData) : [];
        const selectedItem = periodData.find(p => p.PeriodicityTransactionID === selectedPeriod);
        targetInput.value = selectedItem ? selectedItem.TargetValue : "";
    });
});
</script>



<script>
document.addEventListener("DOMContentLoaded", function () {
    const periodSelect = document.getElementById("Period");
    const targetInput = document.getElementById("Target");

    // Parse data-period-data from dropdown
    const periodData = JSON.parse(periodSelect.dataset.periodData || "[]");

    // Populate dropdown dynamically
    periodSelect.innerHTML = '<option value="">Select</option>';
    periodData.forEach(item => {
        const opt = document.createElement("option");
        opt.value = item.PeriodicityTransactionID;
        opt.textContent = item.PeriodicityTransactionID;
        periodSelect.appendChild(opt);
    });

    // Handle change event
    periodSelect.addEventListener("change", function () {
        const selectedPeriod = this.value;
        const selectedItem = periodData.find(p => p.PeriodicityTransactionID === selectedPeriod);
        targetInput.value = selectedItem ? selectedItem.TargetValue : "";
    });
});
</script>






this is my data 

data-period-data="[{&quot;PeriodicityTransactionID&quot;:&quot;2008&quot;,&quot;TargetValue&quot;:&quot;90%&quot;},{&quot;PeriodicityTransactionID&quot;:&quot;2009&quot;,&quot;TargetValue&quot;:&quot;70%&quot;},{&quot;PeriodicityTransactionID&quot;:&quot;2010&quot;,&quot;TargetValue&quot;:&quot;70%&quot;},{&quot;PeriodicityTransactionID&quot;:&quot;2011&quot;,&quot;TargetValue&quot;:&quot;70%&quot;},{&quot;PeriodicityTransactionID&quot;:&quot;2012&quot;,&quot;TargetValue&quot;:&quot;80%&quot;},{&quot;PeriodicityTransactionID&quot;:&quot;2013&quot;,&quot;TargetValue&quot;:&quot;70%&quot;},{&quot;PeriodicityTransactionID&quot;:&quot;2016&quot;,&quot;TargetValue&quot;:&quot;70%&quot;},{&quot;PeriodicityTransactionID&quot;:&quot;2017&quot;,&quot;TargetValue&quot;:&quot;70%&quot;},{&quot;PeriodicityTransactionID&quot;:&quot;2018&quot;,&quot;TargetValue&quot;:&quot;70%&quot;},{&quot;PeriodicityTransactionID&quot;:&quot;2019&quot;,&quot;TargetValue&quot;:&quot;70%&quot;},{&quot;PeriodicityTransactionID&quot;:&quot;2020&quot;,&quot;TargetValue&quot;:&quot;70%&quot;},{&quot;PeriodicityTransactionID&quot;:&quot;2021&quot;,&quot;TargetValue&quot;:&quot;70%&quot;},{&quot;PeriodicityTransactionID&quot;:&quot;2022&quot;,&quot;TargetValue&quot;:&quot;60%&quot;},{&quot;PeriodicityTransactionID&quot;:&quot;2023&quot;,&quot;TargetValue&quot;:&quot;70%&quot;},{&quot;PeriodicityTransactionID&quot;:&quot;2024&quot;,&quot;TargetValue&quot;:&quot;70%&quot;},{&quot;PeriodicityTransactionID&quot;:&quot;2025&quot;,&quot;TargetValue&quot;:&quot;70%&quot;},{&quot;PeriodicityTransactionID&quot;:&quot;2026&quot;,&quot;TargetValue&quot;:&quot;50%&quot;}]


every period has their value i want to show according to dropdown means if 2008 is selected then show in target texbox value of 2008 
