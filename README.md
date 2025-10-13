this is my controller 

public async Task<IActionResult> CreateKPI(Guid? id, int page = 1, string searchString = "")
{
    if (HttpContext.Session.GetString("Session") != null)
    {
        var UserId = HttpContext.Session.GetString("Session");

        ViewBag.user = User;


        var userIdString = HttpContext.Session.GetString("Session");
        if (string.IsNullOrEmpty(userIdString))
        {
            return RedirectToAction("AccessDenied", "TPR");
        }



        string formName = "CreateKPI";
        var form = await context.AppFormDetails
            .Where(f => f.FormName == formName)
            .Select(f => f.Id)
            .FirstOrDefaultAsync();

        if (form == default)
        {
            return RedirectToAction("AccessDenied", "TPR");
        }

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
        var query = context.AppKpiMasters.AsQueryable();

        if (!string.IsNullOrEmpty(searchString))
        {
            query = query.Where(a => a.KPILevel.Contains(searchString));
        }

        var pagedData = await query.Skip((page - 1) * pageSize).Take(pageSize).ToListAsync();
        var totalCount = query.Count();

        ViewBag.ListData2 = pagedData;
        ViewBag.CurrentPage = page;
        ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
        ViewBag.searchString = searchString;

        var Area = GetAreaDD();
        ViewBag.Area = Area;

        var type = GetKPITypeDD();
        ViewBag.Type = type;

        var unit = GetUnitDD();
        ViewBag.Unit = unit;

        var Periodicity = GetPeriodicityDD();
        ViewBag.Periodicity = Periodicity;

        var perforamnce = GetPerformanceDD();
        ViewBag.perforamnce = perforamnce;

        var division = GetDivisionDD();
        ViewBag.division = division;

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
