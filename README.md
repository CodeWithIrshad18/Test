else
{
    if (!canModify)
        return RedirectToAction("AccessDenied", "TPR");

    var existingRecord = await context.AppKpiMasters.FindAsync(model.ID);
    if (existingRecord == null)
        return NotFound("Record not found.");

    // --- Update editable fields ---
    existingRecord.KPICode = model.KPICode;
    existingRecord.ImmediateSuperior = model.ImmediateSuperior;
    existingRecord.HOD = model.HOD;
    existingRecord.KPISPOC = model.KPISPOC;
    existingRecord.KPIDetails = model.KPIDetails;
    existingRecord.GoodPerformance = model.GoodPerformance;
    existingRecord.NoofDecimal = model.NoofDecimal;
    existingRecord.KPIDefination = model.KPIDefination;
    existingRecord.TypeofKPIID = model.TypeofKPIID;
    existingRecord.KPILevel = model.KPILevel;
    existingRecord.PeriodicityID = model.PeriodicityID;
    existingRecord.UnitID = model.UnitID;
    existingRecord.PerspectiveID = model.PerspectiveID;
    existingRecord.CreatedBy = UserId;

    // --- Mode handling ---
    if (model.Deactivate == true)
    {
        // Keep inactive, allow modifying dates
        existingRecord.Deactivate = true;
        existingRecord.DeactivateFrom = model.DeactivateFrom;
        existingRecord.DeactivateTo = model.DeactivateTo;
    }
    else
    {
        // Active mode: clear dates
        existingRecord.Deactivate = false;
        existingRecord.DeactivateFrom = null;
        existingRecord.DeactivateTo = null;
    }

    context.AppKpiMasters.Update(existingRecord);
    await context.SaveChangesAsync();

    TempData["Success"] = "KPI updated successfully!";
    return RedirectToAction("CreateKPI");
}

       
       
       
       [HttpPost]
       public async Task<IActionResult> CreateKPI(AppKpiMaster model, string actionType)
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


           if (actionType == "save")
           {

               if (model.ID == Guid.Empty)
               {
                   if (!canWrite)
                       return RedirectToAction("AccessDenied", "TPR");

                   var divValue = Request.Form["Division"].ToString();
                   var deptValue = Request.Form["Department"].ToString();
                   var secValue = Request.Form["Section"].ToString();

                   var divisions = divValue.Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);
                   var departments = deptValue.Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);
                   var sections = secValue.Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);

                   var kpiList = new List<AppKpiMaster>();

                   using (var connection = new SqlConnection(GetConnection()))
                   {
                       var dtDiv = connection.Query<(string, Division)>(@"
                   SELECT DISTINCT ema_exec_head_desc AS Division 
                   FROM SAPHRDB.dbo.T_Empl_All 
                   WHERE ema_exec_head_desc IS NOT NULL").ToList();

                       var dtDept = connection.Query<(string Division, string Department)>(@"
                   SELECT DISTINCT ema_exec_head_desc AS Division, ema_dept_desc AS Department 
                   FROM SAPHRDB.dbo.T_Empl_All 
                   WHERE ema_exec_head_desc IS NOT NULL AND ema_dept_desc IS NOT NULL").ToList();

                       var dtSec = connection.Query<(string Division, string Department, string Section)>(@"
                   SELECT DISTINCT ema_exec_head_desc AS Division, ema_dept_desc AS Department, ema_section_desc AS Section
                   FROM SAPHRDB.dbo.T_Empl_All
                   WHERE ema_exec_head_desc IS NOT NULL 
                         AND ema_dept_desc IS NOT NULL 
                         AND ema_section_desc IS NOT NULL").ToList();

                       if (model.Deactivate == true)
                       {
                           // Inactive mode
                           model.DeactivateFrom = model.DeactivateFrom;
                           model.DeactivateTo = model.DeactivateTo;
                       }
                       else
                       {
                           // Active mode
                           model.Deactivate = false;
                           model.DeactivateFrom = null;
                           model.DeactivateTo = null;
                       }


                       foreach (var div in divisions)
                       {
                           var relatedDepts = dtDept
                               .Where(x => x.Division == div && departments.Contains(x.Department))
                               .Select(x => x.Department)
                               .ToList();

                           if (relatedDepts.Count == 0)
                           {
                               kpiList.Add(BuildKPI(model, UserId, div));
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
                                   kpiList.Add(BuildKPI(model, UserId, div, dep));
                               }
                               else
                               {
                                   foreach (var sec in relatedSecs)
                                   {
                                       kpiList.Add(BuildKPI(model, UserId, div, dep, sec));
                                   }
                               }
                           }
                       }

                   }
                   foreach (var kpi in kpiList)
                   {
                       await context.AppKpiMasters.AddRangeAsync(kpi);
                       await context.SaveChangesAsync();
                   }


                   TempData["Success"] = $"{kpiList.Count} KPI(s) created successfully!";
                   return RedirectToAction("CreateKPI");
               }


               else
               {
                   if (!canModify)
                       return RedirectToAction("AccessDenied", "TPR");

                   var existingRecord = await context.AppKpiMasters.FindAsync(model.ID);
                   if (existingRecord == null)
                       return NotFound("Record not found.");

                   existingRecord.KPICode = model.KPICode;
                   existingRecord.ImmediateSuperior = model.ImmediateSuperior;
                   existingRecord.HOD = model.HOD;
                   existingRecord.KPISPOC = model.KPISPOC;
                   existingRecord.KPIDetails = model.KPIDetails;
                   existingRecord.GoodPerformance = model.GoodPerformance;
                   existingRecord.NoofDecimal = model.NoofDecimal;
                   existingRecord.KPIDefination = model.KPIDefination;
                   existingRecord.TypeofKPIID = model.TypeofKPIID;
                   existingRecord.KPILevel = model.KPILevel;
                   existingRecord.PeriodicityID = model.PeriodicityID;
                   existingRecord.UnitID = model.UnitID;
                   existingRecord.PerspectiveID = model.PerspectiveID;
                   existingRecord.CreatedBy = UserId;
                 
                   context.AppKpiMasters.Update(existingRecord);
                   await context.SaveChangesAsync();

                   TempData["Success"] = "KPI updated successfully!";
                   return RedirectToAction("CreateKPI");
               }
           }

           return BadRequest("Invalid action.");
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
               Company = model.Company,
               KPISPOC = model.KPISPOC,
               ImmediateSuperior = model.ImmediateSuperior,
               HOD = model.HOD,
               Deactivate = model.Deactivate ?? false,
               DeactivateFrom = model.Deactivate == true ? model.DeactivateFrom : null,
               DeactivateTo = model.Deactivate == true ? model.DeactivateTo : null


           };
       }
