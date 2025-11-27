LEFT JOIN (
    SELECT t1.*
    FROM App_TargetSetting_NOPR t1
    INNER JOIN (
        SELECT KPIID, MAX(ID) AS MaxID
        FROM App_TargetSetting_NOPR
        GROUP BY KPIID
    ) t2 ON t1.KPIID = t2.KPIID AND t1.ID = t2.MaxID
) TR ON TR.KPIID = KI.ID

                
                
                
                
                int pageSize = 10;
                int skip = (page - 1) * pageSize;

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
    KID.TypeofKPI,
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
LEFT JOIN App_TypeofKPI_NOPR KID ON KI.TypeofKPIID = KID.ID
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
AND (@search4 IS NULL OR TR.FinYearID LIKE '%' + @search4 + '%')
ORDER BY KI.KPIDetails
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;
";

                    string countQuery = @"
        SELECT COUNT(*) 
FROM App_KPIMaster_NOPR k
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
LEFT JOIN App_TargetSetting_NOPR TR ON TR.KPIID = K.ID
WHERE
k.KPISPOC =@UserId AND k.Deactivate is null AND
    (@search IS NULL OR k.KPICode LIKE '%' + @search + '%' OR u.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%')
AND (@search1 IS NULL OR K.Division LIKE '%' + @search1 + '%')
AND (@search3 IS NULL OR k.KPIDetails LIKE '%' + @search3 + '%')
AND (@search4 IS NULL OR TR.FinYearID LIKE '%' + @search4 + '%');
    ";

                    var pagedData = await connection.QueryAsync<KpiListDto>(sqlQuery, new
                    {
                        UserId,
                        search = string.IsNullOrEmpty(searchString) ? null : searchString,
                        search1 = string.IsNullOrEmpty(Div) ? null : Div,
                        search2 = string.IsNullOrEmpty(Dept) ? null : Dept,
                        search3 = string.IsNullOrEmpty(KPI) ? null : KPI,
                        search4 = string.IsNullOrEmpty(FinyearID) ? null : FinyearID,
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
                        search4 = string.IsNullOrEmpty(FinyearID) ? null : FinyearID
                    });

                    ViewBag.ListData2 = pagedData.ToList();
                    ViewBag.CurrentPage = page;
                    ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
                    ViewBag.searchString = searchString;
                    ViewBag.Dept = Dept;
                    ViewBag.Div = Div;
                    ViewBag.KPI = KPI;
                    ViewBag.FinyearID = FinyearID;
                  
                }

                    <div class="text-center mt-3">
    @if (ViewBag.TotalPages > 1)
    {
        <nav aria-label="Page navigation" style="font-size:12px;" class="d-flex justify-content-center">
            <ul class="pagination">


                <li class="page-item @(ViewBag.CurrentPage == 1 ? "disabled" : "")">
                    <a class="page-link"
                       href="?page=1&searchString=@ViewBag.searchString&Dept=@ViewBag.Dept&KPI=@ViewBag.KPI&Div=@ViewBag.Div">
                        « First
                    </a>
                </li>

                <li class="page-item @(ViewBag.CurrentPage == 1 ? "disabled" : "")">
                    <a class="page-link"
                       href="?page=@(ViewBag.CurrentPage - 1)&searchString=@ViewBag.searchString&Dept=@ViewBag.Dept&KPI=@ViewBag.KPI&Div=@ViewBag.Div">
                        ‹ Prev
                    </a>
                </li>

              
                @for (int i = Math.Max(1, ViewBag.CurrentPage - 1); i <= Math.Min(ViewBag.CurrentPage + 1, ViewBag.TotalPages); i++)
                {
                    <li class="page-item @(ViewBag.CurrentPage == i ? "active" : "")">
                        <a class="page-link"
                           href="?page=@i&searchString=@ViewBag.searchString&Dept=@ViewBag.Dept&KPI=@ViewBag.KPI&Div=@ViewBag.Div">
                            @i
                        </a>
                    </li>
                }

 
                <li class="page-item @(ViewBag.CurrentPage == ViewBag.TotalPages ? "disabled" : "")">
                    <a class="page-link"
                       href="?page=@(ViewBag.CurrentPage + 1)&searchString=@ViewBag.searchString&Dept=@ViewBag.Dept&KPI=@ViewBag.KPI&Div=@ViewBag.Div">
                        Next ›
                    </a>
                </li>

  
                <li class="page-item @(ViewBag.CurrentPage == ViewBag.TotalPages ? "disabled" : "")">
                    <a class="page-link"
                       href="?page=@ViewBag.TotalPages&searchString=@ViewBag.searchString&Dept=@ViewBag.Dept&KPI=@ViewBag.KPI&Div=@ViewBag.Div">
                        Last »
                    </a>
                </li>

            </ul>
        </nav>
    }
</div>
    </div>
