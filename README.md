var userType = await context.App_PermissionMaster
    .Where(x => x.Pno == UserId)
    .Select(x => x.Type)
    .FirstOrDefaultAsync();

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
    ts.Comparative,
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
LEFT JOIN App_TypeofKPI_NOPR t ON k.TypeofKPIID = t.ID
LEFT JOIN App_GoodPerformance_NOPR g ON k.GoodPerformance = g.ID
LEFT JOIN App_TargetSetting_NOPR ts ON k.ID = ts.KPIID
LEFT JOIN App_PermissionMaster pm ON k.KPISPOC = pm.Pno
WHERE
    ( 
        @UserType = 'Admin'
        OR k.KPISPOC = @UserId
    )
AND (k.Deactivate IS NULL OR k.Deactivate = 0)
AND (@search IS NULL OR k.KPICode LIKE '%' + @search + '%' OR u.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%')
AND (@search1 IS NULL OR k.Division LIKE '%' + @search1 + '%')
AND (@search3 IS NULL OR k.KPIDetails LIKE '%' + @search3 + '%')
AND (@search4 IS NULL OR ts.FinYearID LIKE '%' + @search4 + '%')
ORDER BY k.KPIDetails
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;
";

      
var pagedData = await connection.QueryAsync<KpiListDto>(sqlQuery, new
{
    UserId,
    UserType = userType,
    search = string.IsNullOrEmpty(searchString) ? null : searchString,
    search1 = string.IsNullOrEmpty(Div) ? null : Div,
    search2 = string.IsNullOrEmpty(Dept) ? null : Dept,
    search3 = string.IsNullOrEmpty(KPI) ? null : KPI,
    search4 = string.IsNullOrEmpty(FinYearID) ? null : FinYearID,
    skip,
    take = pageSize
});

string countQuery = @"
SELECT COUNT(*) 
FROM App_KPIMaster_NOPR k
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
LEFT JOIN App_TargetSetting_NOPR ts ON k.ID = ts.KPIID
WHERE
    (
        @UserType = 'Admin'
        OR k.KPISPOC = @UserId
    )
AND (@search IS NULL OR k.KPICode LIKE '%' + @search + '%' OR u.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%')
AND (@search1 IS NULL OR k.Division LIKE '%' + @search1 + '%')
AND (@search4 IS NULL OR ts.FinYearID LIKE '%' + @search4 + '%')
AND (@search3 IS NULL OR k.KPIDetails LIKE '%' + @search3 + '%');
";

var totalCount = await connection.ExecuteScalarAsync<int>(countQuery, new
{
    UserId,
    UserType = userType,
    search = string.IsNullOrEmpty(searchString) ? null : searchString,
    search1 = string.IsNullOrEmpty(Div) ? null : Div,
    search2 = string.IsNullOrEmpty(Dept) ? null : Dept,
    search3 = string.IsNullOrEmpty(KPI) ? null : KPI,
    search4 = string.IsNullOrEmpty(FinYearID) ? null : FinYearID
});


        
        
        [Authorize(Policy = "CanRead")]
        public async Task<IActionResult> TargetKPI(Guid? ID, int page = 1, string searchString = "", string Dept = "", string KPI = "",string Div="",string FinYearID="")
        {
            if (HttpContext.Session.GetString("Session") != null)
            {
                var UserId = HttpContext.Session.GetString("Session");
                ViewBag.user = User;

                if (string.IsNullOrEmpty(UserId))
                    return RedirectToAction("AccessDenied", "TPR");

                string formName = "TargetKPI";
                var form = await context.AppFormDetails
                    .Where(f => f.FormName == formName)
                    .Select(f => f.Id)
                    .FirstOrDefaultAsync();

                if (form == default)
                    return RedirectToAction("AccessDenied", "TPR");

                bool canModify = await context.AppUserFormPermissions
                    .Where(p => p.UserId == UserId && p.FormId == form)
                    .AnyAsync(p => p.AllowModify == true);
                bool canDelete = await context.AppUserFormPermissions
                    .Where(p => p.UserId == UserId && p.FormId == form)
                    .AnyAsync(p => p.AllowDelete == true);
                bool canWrite = await context.AppUserFormPermissions
                    .Where(p => p.UserId == UserId && p.FormId == form)
                    .AnyAsync(p => p.AllowWrite == true);


                ViewBag.CanModify = canModify;
                ViewBag.CanDelete = canDelete;
                ViewBag.CanWrite = canWrite;

                int pageSize = 4;
                int skip = (page - 1) * pageSize;

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
    ts.Comparative,
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
LEFT JOIN App_PermissionMaster pm ON k.KPISPOC = pm.Pno

WHERE
(pm.Type = 'Admin' OR k.KPISPOC = pm.Pno)
    AND (k.Deactivate IS NULL OR k.Deactivate = 0) AND
    (@search IS NULL OR k.KPICode LIKE '%' + @search + '%' OR u.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%')
AND (@search1 IS NULL OR k.Division LIKE '%' + @search1 + '%')
AND (@search3 IS NULL OR k.KPIDetails LIKE '%' + @search3 + '%')
AND (@search4 IS NULL OR ts.FinYearID LIKE '%' + @search4 + '%')

ORDER BY k.KPIDetails
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;";

                    string countQuery = @"
        SELECT COUNT(*) 
FROM App_KPIMaster_NOPR k
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
LEFT JOIN App_TargetSetting_NOPR ts ON k.ID = ts.KPIID
WHERE
KPISPOC =@UserId AND
    (@search IS NULL OR k.KPICode LIKE '%' + @search + '%' OR u.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%')
AND (@search1 IS NULL OR k.Division LIKE '%' + @search1 + '%')
AND (@search4 IS NULL OR ts.FinYearID LIKE '%' + @search4 + '%')
AND (@search3 IS NULL OR k.KPIDetails LIKE '%' + @search3 + '%');

    ";

                    var pagedData = await connection.QueryAsync<KpiListDto>(sqlQuery, new
                    {
                        UserId,
                        search = string.IsNullOrEmpty(searchString) ? null : searchString,                       
                        search1 = string.IsNullOrEmpty(Div) ? null : Div,                       
                        search2 = string.IsNullOrEmpty(Dept) ? null : Dept,
                        search3 = string.IsNullOrEmpty(KPI) ? null : KPI,
                        search4 = string.IsNullOrEmpty(FinYearID) ? null : FinYearID,
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
                        search4 = string.IsNullOrEmpty(FinYearID) ? null : FinYearID
                    });

                    ViewBag.ListData2 = pagedData.ToList();
                    ViewBag.CurrentPage = page;
                    ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
                    ViewBag.searchString = searchString;
                    ViewBag.Dept = Dept;
                    ViewBag.KPI = KPI;
                    ViewBag.Div = Div;
                    ViewBag.FinYearID = FinYearID;
                }



                ViewBag.FinYear = GetFinYearDD();
                ViewBag.department = GetDept();
                var finYears = GetFinYearDD();
                var currentFY = GetCurrentFinYear();
                ViewBag.division = GetDivisionDD();

                ViewBag.FinYearDropdown = finYears;
                ViewBag.CurrentFY = currentFY;




                using (var connection = new SqlConnection(GetConnection()))
                {
                    string query2 = @"
                SELECT DISTINCT ema_exec_head_desc 
                FROM SAPHRDB.dbo.T_Empl_All
                WHERE ema_exec_head_desc IS NOT NULL
                ORDER BY ema_exec_head_desc";

                    var divisions = connection.Query<Division>(query2).ToList();
                    ViewBag.DivisionDropdown = divisions;
                }

                AppTarget viewModel = null;
                if (ID.HasValue)
                {
                    viewModel = await context.AppTargets.FirstOrDefaultAsync(a => a.ID == ID);
                }


                return View(viewModel);
            }
            else
            {
                return RedirectToAction("Login", "User");
            }
        }
