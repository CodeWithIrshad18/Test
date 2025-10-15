this is my three searching 

  <input type="text" name="searchString" class="form-control me-2" value="@ViewBag.searchString" placeholder="🔍 KPI Code..." autocomplete="off" style="height:36px;"/>
 <select class="form-control form-control-sm custom-select  me-2" name="Dept" style="height:80%;">
			<option value="">🔍 Department...</option>
			@foreach (var item in departmentDropdown)
			{
				<option value="@item.ema_dept_desc" selected="@(item.ema_dept_desc == ViewBag.Dept ? "selected" : null)">@item.ema_dept_desc</option>
			}
			
			</select>

      <input type="text" name="KPI" class="form-control me-2" value="@ViewBag.KPI" placeholder="🔍 KPI..." autocomplete="off" style="height:36px;"/>


and this is my controller 

    public async Task<IActionResult> CreateKPI(Guid? id, int page = 1, string searchString = "",string Dept="",string KPI = "")
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

            int pageSize = 4;
            int skip = (page - 1) * pageSize;

            using (var connection = new SqlConnection(GetSAPConnectionString()))
            {
                string sqlQuery = @"
    SELECT 
        k.ID,
        k.KPIDetails,
        ps.Perspectives,
        u.UnitCode,
        k.KPILevel,
        p.PeriodicityName,
        k.Division,
        k.Department,
        k.Section,
        g.Name,
        k.KPICode,
        k.TypeofKPIID,
        k.KPIDefination,
        k.Company,
        k.GoodPerformance,
        k.PeriodicityID,
        k.UnitID,
        k.PerspectiveID,
        k.NoofDecimal
    FROM App_KPIMaster_NOPR k
    LEFT JOIN App_PeriodicityMaster_NOPR p ON k.PeriodicityID = p.ID
    LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
    LEFT JOIN App_Prespectives_NOPR ps ON k.PerspectiveID = ps.ID
    LEFT JOIN App_TypeofKPI_NOPR t ON k.TypeofKPIID = t.ID
    LEFT JOIN App_GoodPerformance_NOPR g ON k.GoodPerformance = g.ID
    WHERE (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%' 
                        OR k.KPICode LIKE '%' + @search + '%'
                        OR k.KPIDetails LIKE '%' + @search3 + '%'
                        OR u.UnitCode LIKE '%' + @search + '%')
    ORDER BY k.KPIDetails
    OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;
";

                string countQuery = @"
    SELECT COUNT(*) 
    FROM App_KPIMaster_NOPR k
    LEFT JOIN App_Prespectives_NOPR ps ON k.PerspectiveID = ps.ID
    LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
    WHERE (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%'
                        OR k.KPICode LIKE '%' + @search + '%'
                        OR k.KPIDetails LIKE '%' + @search3 + '%'
                        OR u.UnitCode LIKE '%' + @search + '%');
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



            ViewBag.Area = GetAreaDD();
            ViewBag.Type = GetKPITypeDD();
            ViewBag.Unit = GetUnitDD();
            ViewBag.Periodicity = GetPeriodicityDD();
            ViewBag.perforamnce = GetPerformanceDD();
            ViewBag.division = GetDivisionDD();
            ViewBag.department = GetDept();

          
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


first two searching is worked but third is not working 
