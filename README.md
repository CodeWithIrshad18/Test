[HttpPost]
public async Task<IActionResult> CreateKPI(AppKpiMaster model, string actionType)
{
    var userIdString = HttpContext.Session.GetString("Session");
    if (string.IsNullOrEmpty(userIdString))
        return RedirectToAction("AccessDenied", "TPR");

    string formName = "CreateKPI";
    var form = await context.AppFormDetails
        .Where(f => f.FormName == formName)
        .Select(f => f.Id)
        .FirstOrDefaultAsync();

    if (form == default)
        return RedirectToAction("AccessDenied", "TPR");

    bool canModify = await context.AppUserFormPermissions
        .AnyAsync(p => p.UserId == userIdString && p.FormId == form && p.AllowModify);
    bool canDelete = await context.AppUserFormPermissions
        .AnyAsync(p => p.UserId == userIdString && p.FormId == form && p.AllowDelete);
    bool canWrite = await context.AppUserFormPermissions
        .AnyAsync(p => p.UserId == userIdString && p.FormId == form && p.AllowWrite);

    if (actionType == "save")
    {
        if (!canWrite)
            return RedirectToAction("AccessDenied", "TPR");

        // 🔹 Step 1: Build valid Division–Dept–Section matrix dynamically
        var matrix = await BuildKpiMatrixAsync(model.Division, model.Department, model.Section);

        if (matrix.Count == 0)
        {
            TempData["Error"] = "No valid combinations found for selected Division/Department/Section.";
            return RedirectToAction("CreateKPI");
        }

        // 🔹 Step 2: Insert each valid combination as a separate KPI record
        foreach (var (division, department, section) in matrix)
        {
            var newKpi = new AppKpiMaster
            {
                ID = Guid.NewGuid(),
                Company = model.Company,
                Division = division,
                Department = department,
                Section = section,
                KPICode = model.KPICode,
                KPIDetails = model.KPIDetails,
                UnitID = model.UnitID,
                CreatedBy = userIdString,
                PeriodicityID = model.PeriodicityID,
                GoodPerformance = model.GoodPerformance,
                HistoricalBest = model.HistoricalBest,
                HistoricalBestYear = model.HistoricalBestYear,
                TheoreticalBest = model.TheoreticalBest,
                NoofDecimal = model.NoofDecimal,
                KPIDefination = model.KPIDefination,
                PerspectiveID = model.PerspectiveID,
                TypeofKPIID = model.TypeofKPIID,
                LTPSTPID = model.LTPSTPID,
                Central_Local = model.Central_Local,
                KPIUpto = model.KPIUpto,
                Deactivate = model.Deactivate,
                DeactivateOn = model.DeactivateOn,
                KPILevel = model.KPILevel,
                KPIMode = model.KPIMode,
                SourceData = model.SourceData,
                COMode = model.COMode,
                PCNCode = model.PCNCode
            };

            context.AppKpiMasters.Add(newKpi);
        }

        await context.SaveChangesAsync();

        TempData["Success"] = $"{matrix.Count} KPI record(s) saved successfully!";
        return RedirectToAction("CreateKPI");
    }
    else if (actionType == "delete")
    {
        if (!canDelete)
            return RedirectToAction("AccessDenied", "TPR");

        var KPI = await context.AppKpiMasters.FindAsync(model.ID);
        if (KPI == null)
            return NotFound("Record not found.");

        context.AppKpiMasters.Remove(KPI);
        await context.SaveChangesAsync();
        TempData["Delete"] = "KPI deleted successfully!";
        return RedirectToAction("CreateKPI");
    }

    return BadRequest("Invalid action.");
}

private async Task<List<(string Division, string Department, string Section)>> BuildKpiMatrixAsync(
    string dib, string dept, string sec)
{
    var selectedDivisions = dib?.Split(',', StringSplitOptions.RemoveEmptyEntries).Select(x => x.Trim()).ToList() ?? new();
    var selectedDepartments = dept?.Split(',', StringSplitOptions.RemoveEmptyEntries).Select(x => x.Trim()).ToList() ?? new();
    var selectedSections = sec?.Split(',', StringSplitOptions.RemoveEmptyEntries).Select(x => x.Trim()).ToList() ?? new();

    // Fetch valid hierarchy data from your DB
    var hierarchyData = await context.AppKpiHierarchies
        .Select(x => new
        {
            Division = x.ema_exec_head_desc,
            Department = x.ema_dept_desc,
            Section = x.ema_section_desc
        })
        .ToListAsync();

    // Filter only valid matches based on user selections
    var filtered = hierarchyData
        .Where(x =>
            selectedDivisions.Contains(x.Division) &&
            selectedDepartments.Contains(x.Department) &&
            selectedSections.Contains(x.Section))
        .Select(x => (x.Division, x.Department, x.Section))
        .ToList();

    return filtered;
}

public class AppKpiHierarchy
{
    public string ema_exec_head_desc { get; set; }  // Division
    public string ema_dept_desc { get; set; }       // Department
    public string ema_section_desc { get; set; }    // Section
}



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
