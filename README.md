<script>
document.addEventListener('DOMContentLoaded', function () {
    const form = document.getElementById('form');
    const groups = document.querySelectorAll('.external-comparative-group');
    const refNoLinks = document.querySelectorAll('.refNoLink');
    const yesRadio = document.getElementById('Yes');
    const noRadio = document.getElementById('No');

    // 🔹 Function to show/hide all comparative groups
    function toggleComparativeGroups() {
        if (yesRadio.checked) {
            // Show all groups
            groups.forEach(group => group.style.display = 'block');
        } else if (noRadio.checked) {
            // Hide all groups + reset values
            groups.forEach(group => {
                group.style.display = 'none';

                // Reset dropdowns to "No" and trigger readonly logic
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

    // 🔹 Apply readonly logic for individual dropdowns
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

    // 🔹 Initialize logic on load
    toggleComparativeGroups();

    // 🔹 Add event listeners for Yes/No change
    yesRadio.addEventListener('change', toggleComparativeGroups);
    noRadio.addEventListener('change', toggleComparativeGroups);

    // 🔹 Handle readonly toggling when dropdowns change
    groups.forEach(group => {
        const dropdown = group.querySelector('.external-comparative-select');
        if (dropdown) {
            dropdown.addEventListener('change', function () {
                applyReadOnlyLogic(group);
            });
        }
    });

    // 🔹 Optional ref link logic
    refNoLinks.forEach(link => {
        link.addEventListener('click', function (event) {
            event.preventDefault();
            groups.forEach(group => applyReadOnlyLogic(group));
        });
    });

    // 🔹 Validation logic
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

    // 🔹 If value exists, mark field readonly
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







this is my periods container               
  <div id="periodicityContainer" class="mt-3">
<label class="form-label fw-bold">Period Targets</label>
<div id="periodicityFields" class="row gy-2">
</div>
    
and this is the js 

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
                   name="TargetDetails[${index}].PeriodicityTransactionID" id="Periodicity"
                   value="${periodId}" />

            <input type="text" class="form-control" id="value"
                   name="TargetDetails[${index}].TargetValue" 
                   placeholder="Target" 
                   autocomplete="off"
                   value="${existingValue}">
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

when period value has data then makes the textbox readonly
