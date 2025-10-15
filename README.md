[HttpPost]
public async Task<IActionResult> CreateKPI(AppKpiMaster model, string actionType)
{
    var userId = HttpContext.Session.GetString("Session");
    if (string.IsNullOrEmpty(userId))
        return RedirectToAction("AccessDenied", "TPR");

    if (actionType != "save")
        return BadRequest("Invalid action.");

    // 🧩 1️⃣ Read selected values from the form (comma separated)
    var divValue = Request.Form["Division"].ToString();
    var deptValue = Request.Form["Department"].ToString();
    var secValue = Request.Form["Section"].ToString();

    var divisions = divValue.Split(',', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);
    var departments = deptValue.Split(',', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);
    var sections = secValue.Split(',', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);

    var kpiList = new List<AppKpiMaster>();

    using (var connection = new SqlConnection(GetConnection()))
    {
        // 🧩 2️⃣ Fetch data directly from your SAPHRDB table

        var divQuery = @"SELECT DISTINCT ema_exec_head_desc AS Division 
                         FROM SAPHRDB.dbo.T_Empl_All 
                         WHERE ema_exec_head_desc IS NOT NULL";
        var dtDiv = connection.Query<(string Division)>(divQuery).ToList();

        var deptQuery = @"SELECT DISTINCT ema_exec_head_desc AS Division, ema_dept_desc AS Department 
                          FROM SAPHRDB.dbo.T_Empl_All 
                          WHERE ema_exec_head_desc IS NOT NULL AND ema_dept_desc IS NOT NULL";
        var dtDept = connection.Query<(string Division, string Department)>(deptQuery).ToList();

        var secQuery = @"SELECT DISTINCT ema_exec_head_desc AS Division, ema_dept_desc AS Department, ema_section_desc AS Section
                         FROM SAPHRDB.dbo.T_Empl_All
                         WHERE ema_exec_head_desc IS NOT NULL 
                               AND ema_dept_desc IS NOT NULL 
                               AND ema_section_desc IS NOT NULL";
        var dtSec = connection.Query<(string Division, string Department, string Section)>(secQuery).ToList();

        // 🧩 3️⃣ Apply CreateMatrix-style logic
        foreach (var div in divisions)
        {
            var relatedDepts = dtDept
                .Where(x => x.Division == div && departments.Contains(x.Department))
                .Select(x => x.Department)
                .ToList();

            if (relatedDepts.Count == 0)
            {
                // Division-only
                kpiList.Add(BuildKPI(model, userId, div));
                continue;
            }

            foreach (var dep in relatedDepts)
            {
                var relatedSecs = dtSec
                    .Where(x => x.Division == div && x.Department == dep && sections.Contains(x.Section))
                    .Select(x => x.Section)
                    .ToList();

                if (relatedSecs.Count == 0)
                {
                    // Division + Department only
                    kpiList.Add(BuildKPI(model, userId, div, dep));
                }
                else
                {
                    // Division + Department + Section
                    foreach (var sec in relatedSecs)
                    {
                        kpiList.Add(BuildKPI(model, userId, div, dep, sec));
                    }
                }
            }
        }
    }

    // 🧩 4️⃣ Save all generated KPI records
    await context.AppKpiMasters.AddRangeAsync(kpiList);
    await context.SaveChangesAsync();

    TempData["Success"] = $"{kpiList.Count} KPI(s) generated successfully!";
    return RedirectToAction("CreateKPI");
}


// 🔧 Helper for cleaner KPI creation
private AppKpiMaster BuildKPI(AppKpiMaster model, string userId, string div, string dept = null, string sec = null)
{
    return new AppKpiMaster
    {
        ID = Guid.NewGuid(),
        Division = div,
        Department = dept,
        Section = sec,
        KPICode = model.KPICode,
        KPIDetails = model.KPIDetails,
        CreatedBy = userId,
        UnitID = model.UnitID,
        PeriodicityID = model.PeriodicityID,
        GoodPerformance = model.GoodPerformance,
        NoofDecimal = model.NoofDecimal,
        TypeofKPIID = model.TypeofKPIID,
        KPILevel = model.KPILevel,
        PerspectiveID = model.PerspectiveID,
        KPIDefination = model.KPIDefination,
        Company = model.Company
    };
}

  
  
  
  
[HttpGet]
  public IActionResult GetDivisions()
  {
      using (var connection = new SqlConnection(GetConnection()))
      {
          string query = @"
          SELECT DISTINCT ema_exec_head_desc 
          FROM SAPHRDB.dbo.T_Empl_All
          WHERE ema_exec_head_desc IS NOT NULL
          ORDER BY ema_exec_head_desc";

          var divisions = connection.Query<Division>(query).ToList();
          return Json(divisions);
      }
  }

     
  [HttpGet]
  public IActionResult GetDepartments(string division)
  {
      if (string.IsNullOrEmpty(division))
          return Json(new List<Department>());

      using (var connection = new SqlConnection(GetConnection()))
      {
          string query = @"
          SELECT DISTINCT ema_exec_head_desc, ema_dept_desc
          FROM SAPHRDB.dbo.T_Empl_All
          WHERE ema_exec_head_desc = @division
                AND ema_dept_desc IS NOT NULL
          ORDER BY ema_dept_desc";

          var departments = connection.Query<Department>(query, new { division }).ToList();
          return Json(departments);
      }
  }

 
  [HttpGet]
  public IActionResult GetSections(string division, string department)
  {
      if (string.IsNullOrEmpty(division) || string.IsNullOrEmpty(department))
          return Json(new List<Section>());

      using (var connection = new SqlConnection(GetConnection()))
      {
          string query = @"
          SELECT DISTINCT ema_exec_head_desc, ema_dept_desc, ema_section_desc
          FROM SAPHRDB.dbo.T_Empl_All
          WHERE ema_exec_head_desc = @division
                AND ema_dept_desc = @department
                AND ema_section_desc IS NOT NULL
          ORDER BY ema_section_desc";

          var sections = connection.Query<Section>(query, new { division, department }).ToList();
          return Json(sections);
      }
  }
