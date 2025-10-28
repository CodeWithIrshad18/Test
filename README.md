<script>
document.addEventListener('DOMContentLoaded', function () {
    const KPIMaster = document.getElementById("form");
    const refNoLinks = document.querySelectorAll(".refNoLink");
    const deleteButton = document.getElementById("deleteButton");
    const submitButton = document.getElementById("submitButton");
    const actionTypeInput = document.getElementById("actionType");

    refNoLinks.forEach(link => {
        link.addEventListener("click", async function (event) {
            event.preventDefault();
            KPIMaster.style.display = "block";

            // Basic form field population
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

            // ✅ Get TSID from the clicked element
            const tsid = this.dataset.tsid;

            if (tsid) {
                try {
                    const response = await fetch(`/YourController/GetTargetDetails?TSID=${tsid}`);
                    const data = await response.json();

                    // Populate dropdown
                    const periodSelect = document.getElementById("Period");
                    periodSelect.innerHTML = '<option value="">Select</option>';

                    data.forEach(item => {
                        const option = document.createElement("option");
                        option.value = item.period;
                        option.textContent = item.period;
                        periodSelect.appendChild(option);
                    });

                    // Set default target (first item)
                    if (data.length > 0) {
                        document.getElementById("Target").value = data[0].targetValue;
                    }

                    // Save data locally to reuse when Period changes
                    periodSelect.dataset.periodData = JSON.stringify(data);
                } catch (error) {
                    console.error("Error fetching target details:", error);
                }
            }

            // ✅ Button actions
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

    // ✅ Handle change in Period dropdown
    const periodSelect = document.getElementById("Period");
    periodSelect.addEventListener("change", function () {
        const selectedPeriod = this.value;
        const periodData = this.dataset.periodData ? JSON.parse(this.dataset.periodData) : [];

        const selectedItem = periodData.find(x => x.period == selectedPeriod);
        document.getElementById("Target").value = selectedItem ? selectedItem.targetValue : '';
    });
});
</script>




<script>
                            document.addEventListener('DOMContentLoaded', function () {
    const KPIMaster = document.getElementById("form");
    const refNoLinks = document.querySelectorAll(".refNoLink");
    const deleteButton = document.getElementById("deleteButton");
    const submitButton = document.getElementById("submitButton");
    const actionTypeInput = document.getElementById("actionType");


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
                    document.getElementById("form").submit();
                }
            });
        });
    }
});
});
});

</script>
