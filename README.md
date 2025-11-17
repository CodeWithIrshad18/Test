2nd Js
<script>
document.addEventListener('DOMContentLoaded', function () {
    const form = document.getElementById('form');
    const groups = document.querySelectorAll('.external-comparative-group');
    const refNoLinks = document.querySelectorAll('.refNoLink');
    const yesRadio = document.getElementById('Yes');
    const noRadio = document.getElementById('No');


    function toggleComparativeGroups() {
        if (yesRadio.checked) {
   
            groups.forEach(group => group.style.display = 'block');
        } else if (noRadio.checked) {
  
            groups.forEach(group => {
                group.style.display = 'none';

   
                const dropdown = group.querySelector('.external-comparative-select');
                const valueInput = group.querySelector('.comparative-value');
                const detailsInput = group.querySelector('.comparative-details');

                if (dropdown) {
                    dropdown.value = 'No';
                }
                if (valueInput) {
                    valueInput.value = '';
                    valueInput.readOnly = true;
                }
                if (detailsInput) {
                    detailsInput.value = '';
                    detailsInput.readOnly = true;
                }
            });
        }
    }


    function applyReadOnlyLogic(group) {
        const dropdown = group.querySelector('.external-comparative-select');
        const valueInput = group.querySelector('.comparative-value');
        const detailsInput = group.querySelector('.comparative-details');

        if (!dropdown || !valueInput || !detailsInput) return;

        if (dropdown.value === 'No') {
            valueInput.readOnly = true;
            detailsInput.readOnly = true;
            valueInput.value = '';
            detailsInput.value = '';
            valueInput.classList.remove('is-invalid');
            detailsInput.classList.remove('is-invalid');
        } else {
            valueInput.readOnly = false;
            detailsInput.readOnly = false;
        }
    }


    toggleComparativeGroups();


    yesRadio.addEventListener('change', toggleComparativeGroups);
    noRadio.addEventListener('change', toggleComparativeGroups);

    groups.forEach(group => {
        const dropdown = group.querySelector('.external-comparative-select');
        if (dropdown) {
            dropdown.addEventListener('change', function () {
                applyReadOnlyLogic(group);
            });
        }
    });


    refNoLinks.forEach(link => {
        link.addEventListener('click', function (event) {
            event.preventDefault();
            groups.forEach(group => applyReadOnlyLogic(group));
        });
    });

    form.addEventListener('submit', function (event) {
        event.preventDefault();
        let isValid = true;
        const elements = this.querySelectorAll('input, select, textarea');

        elements.forEach(function (element) {
            if (['KPIID', 'CreatedBy', 'KPICode', 'ID', 'value', 'Periodicity'].includes(element.id)) return;

            if (element.readOnly || element.disabled) {
                element.classList.remove('is-invalid');
                return;
            }

            if (element.value.trim() === '') {
                isValid = false;
                element.classList.add('is-invalid');
            } else {
                element.classList.remove('is-invalid');
            }
        });

        if (isValid) {
            form.submit();
        }
    });
});
</script>

first Js

<script>
document.addEventListener('DOMContentLoaded', function () {
    const KPIMaster = document.getElementById("form");
    const refNoLinks = document.querySelectorAll(".refNoLink");
    const deleteButton = document.getElementById("deleteButton");
    const submitButton = document.getElementById("submitButton");
    const actionTypeInput = document.getElementById("actionType");
     const groups = document.querySelectorAll('.external-comparative-group');


    refNoLinks.forEach(link => {
        link.addEventListener("click", async function (event) {
            event.preventDefault();
            KPIMaster.style.display = "block";

            document.getElementById("KPICode").value = this.dataset.kpicode;
            document.getElementById("UnitCode").value = this.dataset.unitcode;
            document.getElementById("KPIDetails").value = this.dataset.kpidetails;
            document.getElementById("KPIID").value = this.dataset.kpiid;
            document.getElementById("ID").value = this.dataset.id;
            document.getElementById("PeriodicityID").value = this.dataset.periodicityid;
            document.getElementById("Relevant_comparative_available").value = this.dataset.relevant_comparative_available;
            document.getElementById("Relevant_comparative_available_Value").value = this.dataset.relevant_comparative_available_value;
            document.getElementById("Relevant_comparative_available_Details").value = this.dataset.relevant_comparative_available_details;
            document.getElementById("Current_performance_better_than_comparative").value = this.dataset.current_performance_better_than_comparative;
            document.getElementById("Current_performance_better_than_comparative_Value").value = this.dataset.current_performance_better_than_comparative_value;
            document.getElementById("Current_performance_better_than_comparative_Details").value = this.dataset.current_performance_better_than_comparative_details;
            document.getElementById("Theoretical_limit_known").value = this.dataset.theoretical_limit_known;
            document.getElementById("Theoretical_limit_known_Value").value = this.dataset.theoretical_limit_known_value;
            document.getElementById("Theoretical_limit_known_Details").value = this.dataset.theoretical_limit_known_details;
            document.getElementById("Statutory_standard_guidline_known").value = this.dataset.statutory_standard_guidline_known;
            document.getElementById("Statutory_standard_guidline_known_Value").value = this.dataset.statutory_standard_guidline_known_value;
            document.getElementById("Statutory_standard_guidline_known_Details").value = this.dataset.statutory_standard_guidline_known_details;
            document.getElementById("Internal_Benchmark_available").value = this.dataset.internal_benchmark_available;
            document.getElementById("Internal_Benchmark_available_Value").value = this.dataset.internal_benchmark_available_value;
            document.getElementById("Internal_Benchmark_available_Details").value = this.dataset.internal_benchmark_available_details;
            document.getElementById("hist1").value = this.dataset.historical_bast_available;
            document.getElementById("hist2").value = this.dataset.historical_bast_available_value;
            document.getElementById("hist3").value = this.dataset.historical_bast_available_details;

            if (deleteButton) deleteButton.style.display = "inline-block";

        
            const comparative = this.dataset.comparative;   

const comparativeYes = document.getElementById("Yes");
const comparativeNo = document.getElementById("No");
const comparativeGroups = document.querySelectorAll('.external-comparative-group');

function showComparative() {
    comparativeGroups.forEach(g => g.style.display = "block");
}

function hideComparative() {
    comparativeGroups.forEach(g => g.style.display = "none");

    document.querySelectorAll(".external-comparative-select").forEach(s => s.value = "");
    document.querySelectorAll(".comparative-value").forEach(i => i.value = "");
    document.querySelectorAll(".comparative-details").forEach(i => i.value = "");
}

if (comparative === "True") {
    comparativeYes.checked = true;
    showComparative();
}
else if (comparative === "False") {
    comparativeNo.checked = true;
    hideComparative();
}
else {
    comparativeYes.checked = true;
    showComparative();
}


            const periodicityId = this.dataset.periodicityid;
            const periodicityContainer = document.getElementById("periodicityFields");

            periodicityContainer.innerHTML = `
                <div class='text-muted small'>Loading periods...</div>
            `;

           try {
    const response = await fetch(`/TPR/GetPeriodicity?periodicityId=${periodicityId}`);
    const periods = await response.json();

    if (periods.length > 0) {
        periodicityContainer.innerHTML = "";
        const total = periods.length;

        const kpiId = this.dataset.id;
        const targetResponse = await fetch(`/TPR/GetTargetDetails?kpiId=${kpiId}`);
        const targetData = await targetResponse.json();


       const targetMap = {};
targetData.forEach(t => {
  
    const key = t.PeriodicityTransactionID || t.periodicityTransactionID;
    const val = t.TargetValue || t.targetValue;
    targetMap[key] = val;
});

periods.forEach((period, index) => {
    let periodId = period; 
    let periodLabel = period; 

    if (typeof period === "object") {
        periodId = period.id || period.PeriodicityTransactionID || "";
        periodLabel = period.name || period.PeriodicityName || periodId;
    }

    let colClass = "col-md-3"; 
    if (total <= 4) colClass = "col-md-6"; 
    else if (total === 1) colClass = "col-12"; 
    else if (total <= 6) colClass = "col-md-4";

    const existingValue = targetMap[periodId] || "";

    const readOnlyAttr = existingValue ? "readonly" : "";

    const div = document.createElement("div");
    div.className = `${colClass} mb-2`;

    div.innerHTML = `
        <div class="input-group input-group-sm flex-nowrap">
            <span class="input-group-text text-truncate" 
                  style="max-width: 200px;" 
                  title="${periodLabel}">
                ${periodLabel}
            </span>

            <input type="hidden" 
                   name="TargetDetails[${index}].PeriodicityTransactionID" 
                   id="Periodicity"
                   value="${periodId}" />

            <input type="text" class="form-control" id="value"
                   name="TargetDetails[${index}].TargetValue" 
                   placeholder="Target" 
                   autocomplete="off"
                   value="${existingValue}"
                   ${readOnlyAttr}>
        </div>
    `;

    periodicityContainer.appendChild(div);
});

    } else {
        periodicityContainer.innerHTML = `<div class="text-danger small">No periods found for this KPI.</div>`;
    }

} catch (error) {
    periodicityContainer.innerHTML = `<div class="text-danger small">Error loading periodicity data.</div>`;
    console.error(error);
}
        });
    });

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
</script>

for refNoClick when option is selected for No then ignore the validations of that block 
