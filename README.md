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
