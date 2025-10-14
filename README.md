this is my model 

  public partial class AppKpiMaster
  {
      public Guid ID { get; set; }
      public string? Company { get; set; }
      public string? Division { get; set; }
      public string? Department { get; set; }
      public string? Section { get; set; }
      public string? KPICode { get; set; }
      public string? KPIDetails { get; set; }
      public Guid UnitID { get; set; }
      public string? CreatedBy { get; set; }
      public Guid? PeriodicityID { get; set; }
      public string? GoodPerformance { get; set; }
      public decimal? HistoricalBest { get; set; }
      public Guid? HistoricalBestYear { get; set; }
      public decimal? TheoreticalBest { get; set; }
      public int? NoofDecimal { get; set; }
      public string? KPIDefination { get; set; }
      public Guid? PerspectiveID { get; set; }
      public Guid? TypeofKPIID { get; set; }
      public Guid? LTPSTPID { get; set; }
      public string? Central_Local { get; set; }
      public string? KPIUpto { get; set; }
      public bool? Deactivate { get; set; }
      public DateTime? DeactivateOn { get; set; }
      public string? KPILevel { get; set; }
      public string? KPIMode { get; set; }
      public string? SourceData { get; set; }
      public string? COMode { get; set; }
      public string? PCNCode { get; set; }

  }

and this is the controller 

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


i want to store like that 
