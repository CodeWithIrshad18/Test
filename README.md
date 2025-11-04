periodSelect.addEventListener("change", function () {
    const selectedID = this.value;
    const periodData = this.dataset.periodData ? JSON.parse(this.dataset.periodData) : [];

    // 🟡 NEW: If "Select" (empty), hide hold/hold reason completely
    if (!selectedID) {
        holdYes.checked = false;
        holdNo.checked = true;
        holdReasonInput.value = "";
        holdReasonField.forEach(el => el.style.display = "none");
        return;
    }

    const selectedItem = periodData.find(p => p.ID === selectedID);
    if (!selectedItem) return;

    targetInput.value = selectedItem.TargetValue || '';
    document.getElementById("PeriodID").value = selectedItem.ID;

    // 🟢 Show hold fields when a period is selected
    holdReasonField.forEach(el => el.style.display = "block");

    if (selectedItem.Hold === true || selectedItem.Hold === "True" || selectedItem.Hold === 1) {
        holdYes.checked = true;
        holdReasonInput.value = selectedItem.HoldReason || "";
    } else {
        holdNo.checked = true;
        holdReasonInput.value = "";
    }

    toggleHoldFields();
});




[HttpGet]
public async Task<JsonResult> GetTargets(Guid TSID)
{
    using (var connection = new SqlConnection(GetSAPConnectionString()))
    {
        string query = @"
            SELECT 
                pm.ID,
                tj.PeriodicityTransactionID,
                tj.TargetValue,
                t2.Hold,
                t2.HoldReason
            FROM App_KPIMaster_NOPR ts
            INNER JOIN App_TargetSetting_NOPR td
                ON ts.ID = td.KPIID
            INNER JOIN App_TargetSettingDetails_NOPR tj
                ON td.ID = tj.MasterID
            INNER JOIN App_PeriodicityTransaction pm
                ON pm.PeriodicityName = tj.PeriodicityTransactionID
            OUTER APPLY (
                SELECT TOP 1 Hold, HoldReason
                FROM App_KPITransactions_NOPR t
                WHERE t.KPIID = ts.ID 
                  AND t.PeriodTransactionID = pm.ID
                ORDER BY t.CreatedOn DESC
            ) t2
            WHERE td.ID = @TSID
            ORDER BY pm.Sl_no;
        ";

        var result = await connection.QueryAsync(query, new { TSID = TSID });
        return Json(result);
    }
}




this is my GetTargets method 	

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


and this is my output 

ID	PeriodicityTransactionID	TargetValue
87B70B1D-E095-4CA4-A3A8-980EA72A71F7	APR	12
8B80518A-1036-4A2B-87E1-8EE96D0F8C3E	MAY	12
B4476AA4-C638-4FE2-A443-ACBF8391A1D5	JUN	25
6F11230E-8C23-4281-A173-21CF41160E9B	JUL	55
2E32BBB9-42CF-485F-87B4-B92C3D69BA78	AUG	15
C529C8FE-D487-4CCF-A9B6-1AC15C956505	SEP	12
965EBDA8-CA3B-46C6-8E92-265BAC82AE8C	OCT	12
F25AEBE8-30B3-459F-9259-1E977E9D9608	NOV	12
DA700145-A444-43C8-BD04-A36539681EC7	DEC	12
137F9F9F-B669-4A75-91E1-6EA94595DD94	JAN	12
81316DE7-5CDC-4754-9006-E5852C6A8935	FEB	12
23275110-2702-46B5-9CE1-EAB587631660	MAR	12


but there is no hold/unhold here how can we expect that behaviour from that but I have another table which has this value 

ID	KPIID	PeriodTransactionID	Value	FinYearID	CreatedBy	CreatedOn	KPIDate	YTDValue	KPITime	Hold	HoldReason
406991B9-A4A7-4935-B9C4-20F7DF2508E6	DCF05CE1-C101-4933-9B16-C171BDE165EC	DA700145-A444-43C8-BD04-A36539681EC7	12.0000	858C5BF6-2548-4BBE-A7E1-20A159910260	151514	2025-11-04 12:38:40.227	NULL	15.0000	NULL	1	testing for hold
DA956136-996F-40DA-8098-2A0A5042AA86	39A04BE6-D9B5-4090-9842-9CE35C8E1276	87B70B1D-E095-4CA4-A3A8-980EA72A71F7	12.0000	858C5BF6-2548-4BBE-A7E1-20A159910260	151514	2025-11-04 11:05:47.410	NULL	123.0000	NULL	1	test
60FF62B7-1EDE-414F-B72E-57E1E4D78E30	39A04BE6-D9B5-4090-9842-9CE35C8E1276	B4476AA4-C638-4FE2-A443-ACBF8391A1D5	25.0000	858C5BF6-2548-4BBE-A7E1-20A159910260	151514	2025-11-04 12:32:29.637	NULL	25.0000	NULL	1	hold3
5CC2DD6F-D225-4D40-9A2C-932397996672	DCF05CE1-C101-4933-9B16-C171BDE165EC	B4476AA4-C638-4FE2-A443-ACBF8391A1D5	12.0000	858C5BF6-2548-4BBE-A7E1-20A159910260	151514	2025-11-04 12:36:37.480	NULL	12.0000	NULL	1	testing for hold
9161C13B-55CB-4A00-A605-C3DCDB1CF1B8	39A04BE6-D9B5-4090-9842-9CE35C8E1276	2E32BBB9-42CF-485F-87B4-B92C3D69BA78	123.0000	858C5BF6-2548-4BBE-A7E1-20A159910260	151514	2025-11-04 12:20:32.173	NULL	148.0000	NULL	1	testing for hold
