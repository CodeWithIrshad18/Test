error : SqlException: The multi-part identifier "KI.Division" could not be bound.

query :

                using (var connection = new SqlConnection(GetSAPConnectionString()))
                {
                    string sqlQuery = @"
SELECT 
    KI.ID AS KPIID,
    KD.ID,
    KD.Value,
 KD.Hold,
    KD.HoldReason,
KD.PeriodTransactionID,
    KI.Company,
    TR.FinYearID,
    KI.Division,
    TR.ID AS TSID,
    KI.Department,
    KI.Section,
    pm.PeriodicityName,
    KI.KPIDetails,
    KI.UnitID,
    SF.FinYear,
    KI.CreatedBy,
    KI.KPICode,
    KI.PeriodicityID,
    TR.BaseLine,
    TR.Target,
    TR.BenchMarkPatner,
    TR.BenchMarkValue,
    UM.UnitCode,
    KI.NoofDecimal
FROM App_KPIMaster_NOPR KI
LEFT JOIN App_UOM_NOPR UM ON KI.UnitID = UM.ID
LEFT JOIN App_PeriodicityMaster_NOPR pm ON KI.PeriodicityID = pm.ID
LEFT JOIN (
    SELECT 
        k1.*
    FROM App_KPIDetails_NOPR k1
    INNER JOIN (
        SELECT KPIID, MAX(CreatedOn) AS MaxCreatedOn
        FROM App_KPIDetails_NOPR
        GROUP BY KPIID
    ) k2 ON k1.KPIID = k2.KPIID AND k1.CreatedOn = k2.MaxCreatedOn
) KD ON KD.KPIID = KI.ID
LEFT JOIN App_TargetSetting_NOPR TR ON TR.KPIID = KI.ID
LEFT JOIN App_Sys_FinYear SF ON TR.FinYearID = SF.ID
WHERE
    KI.KPISPOC = @UserId 
    AND (KI.Deactivate IS NULL OR KI.Deactivate = 0)
    AND (@search IS NULL OR KI.KPICode LIKE '%' + @search + '%' OR UM.UnitCode LIKE '%' + @search + '%')
    AND (@search2 IS NULL OR KI.Department LIKE '%' + @search2 + '%')
    AND (@search1 IS NULL OR KI.Division LIKE '%' + @search1 + '%')
    AND (@search3 IS NULL OR KI.KPIDetails LIKE '%' + @search3 + '%')
ORDER BY KI.KPIDetails
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;
";

                    string countQuery = @"
        SELECT COUNT(*) 
FROM App_KPIMaster_NOPR k
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
WHERE
k.KPISPOC =@UserId AND k.Deactivate is null AND
    (@search IS NULL OR k.KPICode LIKE '%' + @search + '%' OR u.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%')
AND (@search1 IS NULL OR KI.Division LIKE '%' + @search1 + '%')
AND (@search3 IS NULL OR k.KPIDetails LIKE '%' + @search3 + '%');
    ";

                    var pagedData = await connection.QueryAsync<KpiListDto>(sqlQuery, new
                    {
                        UserId,
                        search = string.IsNullOrEmpty(searchString) ? null : searchString,
                        search1 = string.IsNullOrEmpty(Div) ? null : Div,
                        search2 = string.IsNullOrEmpty(Dept) ? null : Dept,
                        search3 = string.IsNullOrEmpty(KPI) ? null : KPI,
                        //search4 = string.IsNullOrEmpty(FinyearID) ? null : FinyearID,
                        skip,
                        take = pageSize
                    });

                    var totalCount = await connection.ExecuteScalarAsync<int>(countQuery, new
                    {
                        UserId,
                        search = string.IsNullOrEmpty(searchString) ? null : searchString,
                        search1 = string.IsNullOrEmpty(Div) ? null : Div,
                        search2 = string.IsNullOrEmpty(Dept) ? null : Dept,
                        search3 = string.IsNullOrEmpty(KPI) ? null : KPI,
                        //search4 = string.IsNullOrEmpty(FinyearID) ? null : FinyearID
                    });

                    ViewBag.ListData2 = pagedData.ToList();
                    ViewBag.CurrentPage = page;
                    ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
                    ViewBag.searchString = searchString;
                    ViewBag.Dept = Dept;
                    ViewBag.Div = Div;
                    ViewBag.KPI = KPI;
                  
                }

searching filter:

<form method="get" action="@Url.Action("ActualKPI", "TPR")" class="d-flex">
 <select class="form-control form-control-sm custom-select  me-2" name="Div" style="height:80%;">
	<option value="">🔍 Division...</option>
	@foreach (var item in DivisionDropdown)
	{
					<option value="@item.ema_exec_head_desc" selected="@(item.ema_exec_head_desc == ViewBag.Div ? "selected" : null)">@item.ema_exec_head_desc</option>
	}
	
	</select>

<button type="submit" class="btn btn-primary px-4 col-sm-2" style="width:12%;">Search</button>
 </form>
