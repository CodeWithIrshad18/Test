change to this code 
     
<div class="row g-3 mt-1 mb-3 align-items-center">
    <div class="col-md-2">
        <label for="comparative" class="control-label">Comparative</label>
    </div>

    <div class="col-md-1">
        <div class="form-check">
            <input type="radio" class="form-check-input" name="Comparative" id="Yes" value="true" autocomplete="off" checked>
            <label class="form-check-label" for="Yes">Yes</label>
        </div>
    </div>

    <div class="col-md-1">
        <div class="form-check">
            <input type="radio" class="form-check-input" name="Comparative" id="No" value="false" autocomplete="off">
            <label class="form-check-label" for="No">No</label>
        </div>
    </div>
    </div>


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

    comparativeGroups.forEach(g => g.style.display = "block");


    document.querySelectorAll(".external-comparative-select").forEach(s => s.disabled = false);
    document.querySelectorAll(".comparative-value").forEach(i => i.readOnly = false);
    document.querySelectorAll(".comparative-details").forEach(i => i.readOnly = false);
}
else if (comparative === "False") {
    comparativeNo.checked = true;


    comparativeGroups.forEach(g => g.style.display = "none");

 
    document.querySelectorAll(".external-comparative-select").forEach(s => {
        s.value = "No";
        s.disabled = true;      
    });

    document.querySelectorAll(".comparative-value").forEach(i => {
        i.value = "";
        i.readOnly = true;     
    });

    document.querySelectorAll(".comparative-details").forEach(i => {
        i.value = "";
        i.readOnly = true;        
    });
}
else {
    comparativeYes.checked = true;

    comparativeGroups.forEach(g => g.style.display = "block");

    document.querySelectorAll(".external-comparative-select").forEach(s => s.disabled = false);
    document.querySelectorAll(".comparative-value").forEach(i => i.readOnly = false);
    document.querySelectorAll(".comparative-details").forEach(i => i.readOnly = false);
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
