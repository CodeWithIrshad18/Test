public async Task<IActionResult> CreateKPI(Guid? id, int page = 1, string searchString = "")
{
    if (HttpContext.Session.GetString("Session") != null)
    {
        var UserId = HttpContext.Session.GetString("Session");
        ViewBag.user = User;

        if (string.IsNullOrEmpty(UserId))
            return RedirectToAction("AccessDenied", "TPR");

        string formName = "CreateKPI";
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

        int pageSize = 5;
        int skip = (page - 1) * pageSize;

        using (var connection = new SqlConnection(GetConnection()))
        {
            string sqlQuery = @"
                SELECT 
                    k.ID,
                    k.KPIDetails,
                    k.KPILevel,
                    p.PeriodicityName,
                    u.UnitName,
                    ps.PerspectiveName,
                    t.TypeofKPIName,
                    k.Division,
                    k.Department,
                    k.GoodPerformance,
                    k.KPICode
                FROM AppKpiMasters k
                LEFT JOIN AppPeriodicities p ON k.PeriodicityID = p.ID
                LEFT JOIN AppUnits u ON k.UnitID = u.ID
                LEFT JOIN AppPerspectives ps ON k.PerspectiveID = ps.ID
                LEFT JOIN AppTypeofKPIs t ON k.TypeofKPIID = t.ID
                WHERE (@search IS NULL OR k.KPILevel LIKE '%' + @search + '%')
                ORDER BY k.KPIDetails
                OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;
            ";

            string countQuery = @"
                SELECT COUNT(*) 
                FROM AppKpiMasters k
                WHERE (@search IS NULL OR k.KPILevel LIKE '%' + @search + '%');
            ";

            var pagedData = await connection.QueryAsync<KpiListDto>(sqlQuery, new
            {
                search = string.IsNullOrEmpty(searchString) ? null : searchString,
                skip,
                take = pageSize
            });

            var totalCount = await connection.ExecuteScalarAsync<int>(countQuery, new
            {
                search = string.IsNullOrEmpty(searchString) ? null : searchString
            });

            ViewBag.ListData2 = pagedData.ToList();
            ViewBag.CurrentPage = page;
            ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
            ViewBag.searchString = searchString;
        }

        // Dropdowns
        ViewBag.Area = GetAreaDD();
        ViewBag.Type = GetKPITypeDD();
        ViewBag.Unit = GetUnitDD();
        ViewBag.Periodicity = GetPeriodicityDD();
        ViewBag.perforamnce = GetPerformanceDD();
        ViewBag.division = GetDivisionDD();

        // Division dropdown (already using Dapper)
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

        AppKpiMaster viewModel = null;
        if (id.HasValue)
        {
            viewModel = await context.AppKpiMasters.FirstOrDefaultAsync(a => a.ID == id);
        }

        return View(viewModel);
    }
    else
    {
        return RedirectToAction("Login", "User");
    }
}





'DatabaseFacade' does not contain a definition for 'SqlQueryRaw' and no accessible extension method 'SqlQueryRaw' accepting a first argument of type 'DatabaseFacade' could be found (are you missing a using directive or an assembly reference?)
