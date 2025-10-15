this is my main controller method , in this i want to make changes for Update and Delete also which is provided below a sample controller method   
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

      using (var connection = new SqlConnection(GetConnection()))
      {
       

          var divQuery = @"SELECT DISTINCT ema_exec_head_desc AS Division 
                   FROM SAPHRDB.dbo.T_Empl_All 
                   WHERE ema_exec_head_desc IS NOT NULL";
          var dtDiv = connection.Query<(string,Division)>(divQuery).ToList();

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

    
          foreach (var div in divisions)
          {
              var relatedDepts = dtDept
                  .Where(x => x.Division == div && departments.Contains(x.Department))
                  .Select(x => x.Department)
                  .ToList();

              if (relatedDepts.Count == 0)
              {
       
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
          
                      kpiList.Add(BuildKPI(model, userId, div, dep));
                  }
                  else
                  {
                 
                      foreach (var sec in relatedSecs)
                      {
                          kpiList.Add(BuildKPI(model, userId, div, dep, sec));
                      }
                  }
              }
          }
      }

    
      await context.AppKpiMasters.AddRangeAsync(kpiList);
      await context.SaveChangesAsync();

      TempData["Success"] = $"{kpiList.Count} KPI(s) generated successfully!";
      return RedirectToAction("CreateKPI");
  }



  private AppKpiMaster BuildKPI(AppKpiMaster model, string userId, string div, string dept = null, string sec = null)
  {
      return new AppKpiMaster
      {
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

sample controller method 

 [HttpPost]

 public async Task<IActionResult> CreateKPI(AppKpiMaster model, string actionType)
 {
     var user = HttpContext.Session.GetString("Session");

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
         .Where(p => p.UserId == userIdString && p.FormId == form)
         .AnyAsync(p => p.AllowModify == true);

     bool canDelete = await context.AppUserFormPermissions
         .Where(p => p.UserId == userIdString && p.FormId == form)
         .AnyAsync(p => p.AllowDelete == true);
     bool canWrite = await context.AppUserFormPermissions
         .Where(p => p.UserId == userIdString && p.FormId == form)
         .AnyAsync(p => p.AllowWrite == true);

     if (actionType == "save")
     {
         if (!canWrite)
         {
             return RedirectToAction("AccessDenied", "TPR");
         }
         if (model.ID == Guid.Empty)
         {
             model.CreatedBy = user;

             context.AppKpiMasters.Add(model);
         }
         else
         {
             if (!canModify)
             {
                 return RedirectToAction("AccessDenied", "TPR");
             }
             var existingRecord = await context.AppKpiMasters.FindAsync(model.ID);
             if (existingRecord != null)
             {

                 existingRecord.CreatedBy = user;
                 context.AppKpiMasters.Update(existingRecord);
             }
             else
             {
                 return NotFound("Record not found.");
             }
         }
         await context.SaveChangesAsync();
         TempData["Success"] = "KPI Saved successfully!";
         return RedirectToAction("CreateKPI");
     }
     else if (actionType == "delete")
     {
         if (!canDelete)
         {
             return RedirectToAction("AccessDenied", "TPR");
         }
         var KPI = await context.AppKpiMasters.FindAsync(model.ID);
         if (KPI == null)
         {
             return NotFound("Record not found.");
         }

         context.AppKpiMasters.Remove(KPI);
         await context.SaveChangesAsync();
         TempData["Delete"] = "KPI deleted successfully!";
         return RedirectToAction("CreateKPI");
     }

     return BadRequest("Invalid action.");
 }
