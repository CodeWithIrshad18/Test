const response = await fetch(
    `${window.appRoot}TPR/GetPeriodicity?periodicityId=${periodicityId}`
);

const targetResponse = await fetch(
    `${window.appRoot}TPR/GetTargetDetails?kpiId=${kpiId}`
);




const response = await fetch(`/TPR/TPR/GetPeriodicity?periodicityId=${periodicityId}`);

const targetResponse = await fetch(`/TPR/TPR/GetTargetDetails?kpiId=${kpiId}`);
