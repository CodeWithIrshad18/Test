const response = await fetch(`/TPR/TPR/GetPeriodicity?periodicityId=${periodicityId}`);

const targetResponse = await fetch(`/TPR/TPR/GetTargetDetails?kpiId=${kpiId}`);
