 
var UserId = HttpContext.Session.GetString("Session");


                using (var connection = new SqlConnection(GetSAPConnectionString()))
                {
                    string sqlQuery = @"
SELECT DISTINCT
    k.ID,
    ts.*,
    k.KPIDetails,
    u.UnitCode,
    k.UnitID,
    p.PeriodicityName,
    k.KPICode,
    k.Department,
    k.Division,
    k.Section,
    k.PeriodicityID,
    k.UnitID,

    CASE 
        WHEN EXISTS (
            SELECT 1 
            FROM App_TargetSettingDetails_NOPR td 
            WHERE td.MasterId = ts.ID
        ) THEN 'Y'
        ELSE 'N'
    END AS TargetSetStatus

FROM App_KPIMaster_NOPR k
LEFT JOIN App_PeriodicityMaster_NOPR p ON k.PeriodicityID = p.ID
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
LEFT JOIN App_Prespectives_NOPR ps ON k.PerspectiveID = ps.ID
LEFT JOIN App_TypeofKPI_NOPR t ON k.TypeofKPIID = t.ID
LEFT JOIN App_GoodPerformance_NOPR g ON k.GoodPerformance = g.ID
LEFT JOIN App_TargetSetting_NOPR ts ON k.ID = ts.KPIID

WHERE
KPISPOC ='151514' AND
    (@search IS NULL OR k.KPICode LIKE '%' + @search + '%' OR u.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%')
AND (@search3 IS NULL OR k.KPIDetails LIKE '%' + @search3 + '%')

ORDER BY k.KPIDetails
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;";

                    string countQuery = @"
        SELECT COUNT(*) 
FROM App_KPIMaster_NOPR k
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
WHERE
    (@search IS NULL OR k.KPICode LIKE '%' + @search + '%' OR u.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%')
AND (@search3 IS NULL OR k.KPIDetails LIKE '%' + @search3 + '%');

    ";

                    var pagedData = await connection.QueryAsync<KpiListDto>(sqlQuery, new
                    {
                        search = string.IsNullOrEmpty(searchString) ? null : searchString,                       
                        search2 = string.IsNullOrEmpty(Dept) ? null : Dept,
                        search3 = string.IsNullOrEmpty(KPI) ? null : KPI,
                        skip,
                        take = pageSize
                    });

                    var totalCount = await connection.ExecuteScalarAsync<int>(countQuery, new
                    {
                        search = string.IsNullOrEmpty(searchString) ? null : searchString,
                        search2 = string.IsNullOrEmpty(Dept) ? null : Dept,
                        search3 = string.IsNullOrEmpty(KPI) ? null : KPI
                    });

                    ViewBag.ListData2 = pagedData.ToList();
                    ViewBag.CurrentPage = page;
                    ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
                    ViewBag.searchString = searchString;
                    ViewBag.Dept = Dept;
                    ViewBag.KPI = KPI;
                }
