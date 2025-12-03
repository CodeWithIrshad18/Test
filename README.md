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
    (
        @UserType = 'Admin'
        OR KI.KPISPOC = @UserId
    )
AND (KI.Deactivate IS NULL OR KI.Deactivate = 0)
AND (@search IS NULL OR KI.KPICode LIKE '%' + @search + '%' OR UM.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR KI.Department LIKE '%' + @search2 + '%')
AND (@search1 IS NULL OR KI.Division LIKE '%' + @search1 + '%')
AND (@search3 IS NULL OR KI.KPIDetails LIKE '%' + @search3 + '%')
AND (@search4 IS NULL OR TR.FinYearID LIKE '%' + @search4 + '%')
ORDER BY KI.KPIDetails DESC
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;
";

string countQuery = @"
SELECT COUNT(*) 
FROM App_KPIMaster_NOPR k
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
LEFT JOIN App_TargetSetting_NOPR TR ON TR.KPIID = k.ID
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
AND (@search4 IS NULL OR TR.FinYearID LIKE '%' + @search4 + '%');
";
        
        
        
        
[Authorize(Policy = "CanRead")]
        public async Task<IActionResult> ActualKPI(Guid? id, int page = 1, string searchString = "", string Dept = "", string KPI = "",string FinyearID= "",string Div="")
        {

            if (HttpContext.Session.GetString("Session") != null)
            {
                var UserId = HttpContext.Session.GetString("Session");
                ViewBag.user = User;


                var allowedPermissions = await context.AppPermissionMasters
                    .Where(x => x.Pno == UserId)
                          .Select(x => new PermissionInfo
                          {
                              Pno = x.Pno,
                              Type = x.Type
                          })
                          .FirstOrDefaultAsync();

                ViewBag.Permission = allowedPermissions;


                var userType = await context.AppPermissionMasters
  .Where(x => x.Pno == UserId)
  .Select(x => x.Type)
  .FirstOrDefaultAsync();

                if (string.IsNullOrEmpty(UserId))
                    return RedirectToAction("AccessDenied", "TPR");

                string formName = "ActualKPI";
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
ORDER BY KI.KPIDetails DESC
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;
";

                    string countQuery = @"
SELECT COUNT(*) 
FROM App_KPIMaster_NOPR k
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
LEFT JOIN App_TargetSetting_NOPR TR ON TR.KPIID = k.ID
WHERE
k.KPISPOC = @UserId 
AND (k.Deactivate IS NULL OR k.Deactivate = 0)
AND (@search IS NULL OR k.KPICode LIKE '%' + @search + '%' OR u.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%')
AND (@search1 IS NULL OR k.Division LIKE '%' + @search1 + '%')
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


                ViewBag.department = GetDept();
                ViewBag.FinYear = GetFinYearDD();
                ViewBag.division = GetDivisionDD();
                var finYears = GetFinYearDD();
                var currentFY = GetCurrentFinYear();

                ViewBag.FinYearDropdown = finYears;
                ViewBag.CurrentFY = currentFY;

                return View();
            }
            else
            {
                return RedirectToAction("AcessDenied", "TPR");
            }
        }
