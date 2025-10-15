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
