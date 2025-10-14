[HttpPost]
public async Task<IActionResult> CreateKPI(AppKpiMaster model, string actionType)
{
    var userId = HttpContext.Session.GetString("Session");
    if (string.IsNullOrEmpty(userId))
        return RedirectToAction("AccessDenied", "TPR");

    if (actionType != "save")
        return BadRequest("Invalid action.");

    // ✅ Hidden values from form
    var divValue = Request.Form["Division"].ToString();
    var deptValue = Request.Form["Department"].ToString();
    var secValue = Request.Form["Section"].ToString();

    // ✅ Split all by semicolon
    var divisions = divValue.Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);
    var departments = deptValue.Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);
    var sections = secValue.Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);

    var kpiList = new List<AppKpiMaster>();

    // ✅ Build combinations logically based on text patterns
    foreach (var div in divisions)
    {
        // All departments that start with division name
        var relatedDepartments = departments
            .Where(d => d.StartsWith(div, StringComparison.OrdinalIgnoreCase))
            .ToList();

        if (relatedDepartments.Count == 0)
        {
            // No department, add division-level KPI
            kpiList.Add(new AppKpiMaster
            {
                ID = Guid.NewGuid(),
                DivisionName = div,
                KPICode = model.KPICode,
                KPIDetails = model.KPIDetails,
                CreatedBy = userId,
                CreatedDate = DateTime.Now
            });
        }

        foreach (var dept in relatedDepartments)
        {
            // All sections that start with department name
            var relatedSections = sections
                .Where(s => s.StartsWith(dept, StringComparison.OrdinalIgnoreCase))
                .ToList();

            if (relatedSections.Count == 0)
            {
                // No section, add Division–Department
                kpiList.Add(new AppKpiMaster
                {
                    ID = Guid.NewGuid(),
                    DivisionName = div,
                    DepartmentName = dept,
                    KPICode = model.KPICode,
                    KPIDetails = model.KPIDetails,
                    CreatedBy = userId,
                    CreatedDate = DateTime.Now
                });
            }

            foreach (var sec in relatedSections)
            {
                // Division–Department–Section level KPI
                kpiList.Add(new AppKpiMaster
                {
                    ID = Guid.NewGuid(),
                    DivisionName = div,
                    DepartmentName = dept,
                    SectionName = sec,
                    KPICode = model.KPICode,
                    KPIDetails = model.KPIDetails,
                    CreatedBy = userId,
                    CreatedDate = DateTime.Now
                });
            }
        }
    }

    // ✅ Save all
    await context.AppKpiMasters.AddRangeAsync(kpiList);
    await context.SaveChangesAsync();

    TempData["Success"] = $"{kpiList.Count} KPI(s) generated successfully!";
    return RedirectToAction("CreateKPI");
}





HttpPost]
public async Task<IActionResult> CreateKPI(AppKpiMaster model, string actionType)
{
    var userId = HttpContext.Session.GetString("Session");
    if (string.IsNullOrEmpty(userId))
        return RedirectToAction("AccessDenied", "TPR");

    string formName = "CreateKPI";
    var form = await context.AppFormDetails
        .Where(f => f.FormName == formName)
        .Select(f => f.Id)
        .FirstOrDefaultAsync();

    if (form == default)
        return RedirectToAction("AccessDenied", "TPR");

    bool canWrite = await context.AppUserFormPermissions
        .Where(p => p.UserId == userId && p.FormId == form)
        .AnyAsync(p => p.AllowWrite == true);

    if (actionType == "save")
    {
        if (!canWrite)
            return RedirectToAction("AccessDenied", "TPR");

        // ✅ Get hidden field values from the form
        var divisionValue = Request.Form["Division"].ToString();
        var departmentValue = Request.Form["Department"].ToString();
        var sectionValue = Request.Form["Section"].ToString();

        // ✅ Split by ';'
        var divisions = divisionValue.Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);
        var departments = departmentValue.Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);
        var sections = sectionValue.Split(';', StringSplitOptions.RemoveEmptyEntries | StringSplitOptions.TrimEntries);

        // ✅ Generate combinations
        var kpiList = new List<AppKpiMaster>();

        foreach (var div in divisions)
        {
            foreach (var dept in departments)
            {
                foreach (var sec in sections)
                {
                    // Create new KPI entry for each valid combination
                    var newKpi = new AppKpiMaster
                    {
                        ID = Guid.NewGuid(),
                        CompanyID = model.CompanyID,
                        DivisionName = div,      // Add this field if exists in table
                        DepartmentName = dept,   // Add this field if exists in table
                        SectionName = sec,       // Add this field if exists in table
                        KPICode = model.KPICode,
                        KPIDetails = model.KPIDetails,
                        CreatedBy = userId,
                        CreatedDate = DateTime.Now
                    };
                    kpiList.Add(newKpi);
                }
            }
        }

        // ✅ Add all KPIs at once
        context.AppKpiMasters.AddRange(kpiList);
        await context.SaveChangesAsync();

        TempData["Success"] = $"{kpiList.Count} KPI(s) created successfully!";
        return RedirectToAction("CreateKPI");
    }

    return BadRequest("Invalid action.");
}





Division
EPC Amenities
Industrial EPC & O&M- JH & WB


Division	Department
EPC Amenities	EPC Amenities
EPC Amenities	EPC Amenities - Jharkhand
Industrial EPC & O&M- JH & WB	Industrial EPC & O&M- JH & WB
Industrial EPC & O&M- JH & WB	Industrial EPC - JH&WB
Industrial EPC & O&M- JH & WB	Industrial O&M - JH&WB

Division	Department	Section
EPC Amenities	EPC Amenities	Office of EPC Amenities
EPC Amenities	EPC Amenities - Jharkhand	EPC Amenities - JSR
Industrial EPC & O&M- JH & WB	Industrial EPC & O&M- JH & WB	Office of Industrial EPC & O&M-JH & WB
Industrial EPC & O&M- JH & WB	Industrial EPC - JH&WB	Industrial EPC - BOTTP
Industrial EPC & O&M- JH & WB	Industrial EPC - JH&WB	Industrial EPC - BOTTP/CRM Bara
Industrial EPC & O&M- JH & WB	Industrial O&M - JH&WB	Industrial O&M - TSL Roads
Industrial EPC & O&M- JH & WB	Industrial O&M - JH&WB	Industrial O&M - Tube Division
Industrial EPC & O&M- JH & WB	Industrial O&M - JH&WB	Industrial O&M -Operations & Maintenance


string dib = "EPC Amenities,Industrial EPC & O&M- JH & WB";

string dept = "EPC AmenitiesEPC, Amenities - Jharkhand,Industrial EPC & O&M- JH & WB,Industrial EPC - JH&WB,Industrial O&M - JH&WB";

string sec = "Office of EPC Amenities,EPC Amenities - JSR,Office of Industrial EPC & O&M-JH & WB,Industrial EPC - BOTTP,Industrial EPC - BOTTP/CRM Bara,Industrial O&M - TSL Roads,Industrial O&M - Tube Division,Industrial O&M -Operations & Maintenance";

write a c# code to create a DataTable Matrix with string value of dib , dept , sec with all the refrence given above 
sample output
Division	Division	Division
EPC Amenities	EPC Amenities	Office of EPC Amenities
EPC Amenities	EPC Amenities - Jharkhand	EPC Amenities - JSR
Industrial EPC & O&M- JH & WB	Industrial EPC & O&M- JH & WB	Office of Industrial EPC & O&M-JH & WB
Industrial EPC & O&M- JH & WB	Industrial EPC - JH&WB	Industrial EPC - BOTTP
Industrial EPC & O&M- JH & WB	Industrial EPC - JH&WB	Industrial EPC - BOTTP/CRM Bara
Industrial EPC & O&M- JH & WB	Industrial O&M - JH&WB	Industrial O&M - TSL Roads
Industrial EPC & O&M- JH & WB	Industrial O&M - JH&WB	Industrial O&M - Tube Division
Industrial EPC & O&M- JH & WB	Industrial O&M - JH&WB	Industrial O&M -Operations & Maintenance


and this is my dropdowns of Division, Department, Section , i want to store like this matrix in my DB

this is my db which stores data 

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
