[HttpPost]
public async Task<IActionResult> CreateKPI(AppKpiMaster model, string actionType)
{
    var userId = HttpContext.Session.GetString("Session");
    if (string.IsNullOrEmpty(userId))
        return RedirectToAction("AccessDenied", "TPR");

    if (actionType != "save")
        return BadRequest("Invalid action.");

    // Get posted data
    var divValue = Request.Form["Division"].ToString();
    var deptValue = Request.Form["Department"].ToString();
    var secValue = Request.Form["Section"].ToString();

    // Parse values
    var divisions = divValue.Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);
    var deptMappings = deptValue.Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);
    var secMappings = secValue.Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);

    var kpiList = new List<AppKpiMaster>();

    // Loop through each Division
    foreach (var div in divisions)
    {
        // Departments belonging to this Division
        var relatedDepts = deptMappings
            .Where(d => d.StartsWith($"{div}|", StringComparison.OrdinalIgnoreCase))
            .Select(d => d.Split('|')[1])
            .ToList();

        if (!relatedDepts.Any())
        {
            // Division-only entry
            kpiList.Add(BuildKPI(model, userId, div));
            continue;
        }

        // Loop through each Department under the Division
        foreach (var dept in relatedDepts)
        {
            // Sections belonging to this Department
            var relatedSecs = secMappings
                .Where(s => s.StartsWith($"{div}|{dept}|", StringComparison.OrdinalIgnoreCase))
                .Select(s => s.Split('|')[2])
                .ToList();

            if (!relatedSecs.Any())
            {
                // Division + Department entry
                kpiList.Add(BuildKPI(model, userId, div, dept));
                continue;
            }

            // Division + Department + Section entries
            foreach (var sec in relatedSecs)
            {
                kpiList.Add(BuildKPI(model, userId, div, dept, sec));
            }
        }
    }

    // Save to DB
    await context.AppKpiMasters.AddRangeAsync(kpiList);
    await context.SaveChangesAsync();

    TempData["Success"] = $"{kpiList.Count} KPI(s) generated successfully!";
    return RedirectToAction("CreateKPI");
}

// 🔧 Helper method to create KPI object
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




I have this post controller method 

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

and this is my dropdowns which shows Division , Department and Section , I want to store like the sample output shown in the reference Image 
