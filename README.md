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
