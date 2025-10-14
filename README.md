[HttpPost]
public async Task<IActionResult> CreateKPI(AppKpiMaster model, string actionType)
{
    var userId = HttpContext.Session.GetString("Session");
    if (string.IsNullOrEmpty(userId))
        return RedirectToAction("AccessDenied", "TPR");

    if (actionType != "save")
        return BadRequest("Invalid action.");

    // Split hidden field values
    var divisions = Request.Form["Division"].ToString()
        .Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);

    var departments = Request.Form["Department"].ToString()
        .Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);

    var sections = Request.Form["Section"].ToString()
        .Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);

    var kpiList = new List<AppKpiMaster>();

    foreach (var div in divisions)
    {
        foreach (var dept in departments)
        {
            // Find matching sections for this department
            var relatedSections = sections
                .Where(s => s.Contains(dept, StringComparison.OrdinalIgnoreCase))
                .ToList();

            if (relatedSections.Any())
            {
                // Create one KPI per section
                foreach (var sec in relatedSections)
                {
                    kpiList.Add(new AppKpiMaster
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
                    });
                }
            }
            else
            {
                // No matching section — still create one KPI
                kpiList.Add(new AppKpiMaster
                {
                    ID = Guid.NewGuid(),
                    Division = div,
                    Department = dept,
                    Section = null,
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
                });
            }
        }
    }

    await context.AppKpiMasters.AddRangeAsync(kpiList);
    await context.SaveChangesAsync();

    TempData["Success"] = $"{kpiList.Count} KPI(s) generated successfully!";
    return RedirectToAction("CreateKPI");
}




[HttpPost]
public async Task<IActionResult> CreateKPI(AppKpiMaster model, string actionType)
{
    var userId = HttpContext.Session.GetString("Session");
    if (string.IsNullOrEmpty(userId))
        return RedirectToAction("AccessDenied", "TPR");

    if (actionType != "save")
        return BadRequest("Invalid action.");

   
    var divValue = Request.Form["Division"].ToString();
    var deptValue = Request.Form["Department"].ToString();
    var secValue = Request.Form["Section"].ToString();

  
    var divisions = divValue.Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);
    var departments = deptValue.Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);
    var sections = secValue.Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);

    var kpiList = new List<AppKpiMaster>();

  
    foreach (var div in divisions)
    {
       
        var relatedDepartments = departments
            .Where(d => d.StartsWith(div, StringComparison.OrdinalIgnoreCase))
            .ToList();

        if (relatedDepartments.Count == 0)
        {
           
            kpiList.Add(new AppKpiMaster
            {
                ID = Guid.NewGuid(),
                Division = div,
                KPICode = model.KPICode,
                KPIDetails = model.KPIDetails,
                CreatedBy = userId,
                UnitID = model.UnitID,
                PeriodicityID = model.PeriodicityID,
                GoodPerformance = model.GoodPerformance,
                NoofDecimal = model.NoofDecimal,
                TypeofKPIID = model.TypeofKPIID,
                KPILevel = model.KPILevel,
                PerspectiveID =model.PerspectiveID,
                 KPIDefination=model.KPIDefination,
                Company = model.Company

            });
        }

        foreach (var dept in relatedDepartments)
        {
          
            var relatedSections = sections
                .Where(s => s.StartsWith(dept, StringComparison.OrdinalIgnoreCase))
                .ToList();

            if (relatedSections.Count == 0)
            {
               
                kpiList.Add(new AppKpiMaster
                {
                    ID = Guid.NewGuid(),
                    Division = div,
                    Department = dept,
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
                });
            }

            foreach (var sec in relatedSections)
            {
               
                kpiList.Add(new AppKpiMaster
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

                });
            }
        }
    }

  
    await context.AppKpiMasters.AddRangeAsync(kpiList);
    await context.SaveChangesAsync();

    TempData["Success"] = $"{kpiList.Count} KPI(s) generated successfully!";
    return RedirectToAction("CreateKPI");
}



and this is my Division , Department, and Section 

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
