[HttpPost]
public async Task<IActionResult> TargetKPI(AppTarget model, string actionType, List<AppTargetDetails>? targetDetails)
{
    var userId = HttpContext.Session.GetString("Session");
    if (string.IsNullOrEmpty(userId))
        return RedirectToAction("AccessDenied", "TPR");

    string formName = "CreateKPI";
    var formId = await context.AppFormDetails
        .Where(f => f.FormName == formName)
        .Select(f => f.Id)
        .FirstOrDefaultAsync();

    if (formId == default)
        return RedirectToAction("AccessDenied", "TPR");

    bool canModify = await context.AppUserFormPermissions
        .AnyAsync(p => p.UserId == userId && p.FormId == formId && p.AllowModify);
    bool canDelete = await context.AppUserFormPermissions
        .AnyAsync(p => p.UserId == userId && p.FormId == formId && p.AllowDelete);
    bool canWrite = await context.AppUserFormPermissions
        .AnyAsync(p => p.UserId == userId && p.FormId == formId && p.AllowWrite);

    if (actionType == "save")
    {
        if (model.ID == Guid.Empty)
        {
            // New record
            if (!canWrite)
                return RedirectToAction("AccessDenied", "TPR");

            model.ID = Guid.NewGuid();
            model.CreatedBy = userId;

            await context.AppTargets.AddAsync(model);
        }
        else
        {
            // Update record
            if (!canModify)
                return RedirectToAction("AccessDenied", "TPR");

            var existing = await context.AppTargets.FindAsync(model.ID);
            if (existing == null)
                return NotFound();

            context.Entry(existing).CurrentValues.SetValues(model);
        }

        // Save Target Details
        if (targetDetails != null && targetDetails.Any())
        {
            // Remove old details
            var existingDetails = context.AppTargetDetails.Where(d => d.MasterID == model.ID);
            context.AppTargetDetails.RemoveRange(existingDetails);

            // Add new ones
            foreach (var detail in targetDetails)
            {
                detail.ID = Guid.NewGuid();
                detail.MasterID = model.ID;
                detail.CreatedBy = userId;
                detail.CreatedOn = DateTime.Now;

                await context.AppTargetDetails.AddAsync(detail);
            }
        }

        await context.SaveChangesAsync();
        return Json(new { success = true, message = "Data saved successfully." });
    }
    else if (actionType == "delete")
    {
        if (!canDelete)
            return RedirectToAction("AccessDenied", "TPR");

        var existing = await context.AppTargets.FindAsync(model.ID);
        if (existing == null)
            return NotFound();

        var details = context.AppTargetDetails.Where(d => d.MasterID == model.ID);
        context.AppTargetDetails.RemoveRange(details);
        context.AppTargets.Remove(existing);

        await context.SaveChangesAsync();

        return Json(new { success = true, message = "Data deleted successfully." });
    }

    return BadRequest("Invalid action.");
}




this is my sub model for 


TargetDetails 
public class AppTargetDetails
 {
     public Guid ID { get; set; }
     public Guid MasterID { get; set; }
     public string? PeriodicityTransactionID { get; set; }
     public string? TargetValue { get; set; }
     public string? CreatedBy { get; set; }
     public DateTime CreatedOn { get; set; }
 }

this is main table model
 public class AppTarget
 {
     public Guid ID { get; set; }
     public Guid KPIID { get; set; }
     public Guid? FinYearID { get; set; }
     public Guid? BasisOfTarget { get; set; }
     public string? BaseLine { get; set; }
     public string? Target { get; set; }
     public string? BenchMarkPatner { get; set; }
     public string? UCL { get; set; }
     public string? LCL { get; set; }
     public string? CreatedBy { get; set; }
     public string? BenchMarkValue { get; set; }
     public Guid? FinYearID1 { get; set; }
     public string? Target1 { get; set; }
     public Guid? FinYearID2 { get; set; }
     public string? Target2 { get; set; }
     public Guid? FinYearID3 { get; set; }
     public string? Target3 { get; set; }
     public Guid? FinYearID4 { get; set; }
     public string? Target4 { get; set; }
     public Guid? PeriodicityID { get; set; }
     public string? Relevant_comparative_available { get; set; }
     public string? Relevant_comparative_available_Value { get; set; }
     public string? Relevant_comparative_available_Details { get; set; }
     public string? Current_performance_better_than_comparative { get; set; }
     public string? Current_performance_better_than_comparative_Value { get; set; }
     public string? Current_performance_better_than_comparative_Details { get; set; }
     public string? Theoretical_limit_known { get; set; }
     public string? Theoretical_limit_known_Value { get; set; }
     public string? Theoretical_limit_known_Details { get; set; }
     public string? Statutory_standard_guidline_known { get; set; }
     public string? Statutory_standard_guidline_known_Value { get; set; }
     public string? Statutory_standard_guidline_known_Details { get; set; }
     public string? Current_performance_better_than_statutory_standard { get; set; }
     public string? Current_performance_better_than_statutory_standard_Value { get; set; }
     public string? Current_performance_better_than_statutory_standard_Details { get; set; }
     public string? Internal_Benchmark_available { get; set; }
     public string? Internal_Benchmark_available_Value { get; set; }
     public string? Internal_Benchmark_available_Details { get; set; }
     public string? Historical_bast_available { get; set; }
     public string? Historical_bast_available_Value { get; set; }
     public string? Historical_bast_available_Details { get; set; }
 
 }

and this is my form 

   <form asp-action="TargetKPI" asp-controller="TPR" id="form" method="post" style="display:none;">
        <div class="card card-custom mt-4">
            <div class="card-header-custom mb-2">Target KPI</div>
          <input type="hidden" asp-for="ID" id="KPIID" />
                    <input type="hidden" id="actionType" name="actionType" />
            <div class="card-body">
                <div class="row g-3">
                     <div class="col-md-1">

                        <label for="KPIDetails" class="control-label">KPI</label>
                        </div>
                         <div class="col-md-11">
                        <input class="form-control form-control-sm" autocomplete="off" id="KPIDetails" readonly/>
                        </div>
                       
                    </div>
                     <div class="row g-3 mt-1">
                       <div class="col-md-1">
                        <label for="FinYear" class="control-label">Fin Year</label>
                        </div>
                      <div class="col-md-3">
                       <select class="form-control form-control-sm custom-select me-2" name="FinYearID" id="FinYearID" disabled>
    @foreach (var item in finYears)
    {
        var isSelected = (item.FinYear == currentFY);
        <option value="@item.ID" selected="@(isSelected ? "selected" : null)">
            @item.FinYear
        </option>
    }
</select>
@if (selectedFinYear != null)
{
    <input type="hidden" name="FinYearID" value="@selectedFinYear.ID" />
}
                    </div>

                     <div class="col-md-1">
                        <label for="UnitCode" class="control-label">UOM</label>
                     </div>

                     <div class="col-md-3">
                        <input class="form-control form-control-sm col-sm-2" id="UnitCode" name="UnitCode" autocomplete="off" readonly>
                    </div>
                     <div class="col-md-1">
                        <label for="KPICode" class="control-label">KPI Code</label>
                        </div>

                    <div class="col-md-3">
                        <input class="form-control form-control-sm" id="KPICode" autocomplete="off" readonly>
                    </div>
                    </div>
                     <hr>
                     <div class="external-comparative-group">
                      <div class="row g-3 mt-1">
                     <div class="col-md-2">
                        <label for="Relevant_comparative_available" class="control-label">External comparative</label>
                        </div>

                    <div class="col-md-2">
                       
                        <select  class="form-control form-control-sm custom-select external-comparative-select" id="Relevant_comparative_available">
                            <option></option>
                            <option value="Available">Available</option>
                            <option value="Not Available">Not Available</option>                           
                        </select>
                    </div>
                     <div class="col-md-1">

                             </div>
                             <div class="col-md-1">

                             </div>
                    <div class="col-md-2">
                        <label for="Relevant_comparative_available_Value" class="control-label">External comparative Value</label>
                        </div>
                        
                    <div class="col-md-1">
                       
                       <input class="form-control form-control-sm comparative-value" id="Relevant_comparative_available_Value" autocomplete="off">
                    </div>

                    <div class="col-md-1">
                        <label for="Relevant_comparative_available_Details" class="control-label">Details</label>
                        </div>

                    <div class="col-md-2">
                       <input class="form-control form-control-sm comparative-details" id="Relevant_comparative_available_Details" autocomplete="off">
                    </div>
                    </div>
                    </div>
                  
                     <div class="row g-3 mt-1">
                     <div class="col-md-2">
                        <label for="Current_performance_better_than_comparative" class="control-label">Current performance</label>
                        </div>

                    <div class="col-md-2">
                       
                        <select  class="form-control form-control-sm custom-select" id="Current_performance_better_than_comparative">        
                            <option value="Available">Available</option>                            
                        </select>
                    </div>
                     <div class="col-md-1">

                             </div>
                             <div class="col-md-1">

                             </div>
                    <div class="col-md-2">
                        <label for="Current_performance_better_than_comparative_Value" class="control-label">Current performance Value</label>
                        </div>

                    <div class="col-md-1">
                       
                       <input class="form-control form-control-sm" id="Current_performance_better_than_comparative_Value" autocomplete="off">
                    </div>
                    <div class="col-md-1">
                        <label for="Current_performance_better_than_comparative_Details" class="control-label">Details</label>
                        </div>

                    <div class="col-md-2">
                       <input class="form-control form-control-sm" id="Current_performance_better_than_comparative_Details" autocomplete="off">
                    </div>
                    </div>
                     <div class="external-comparative-group">
                    <div class="row g-3 mt-1">
                     <div class="col-md-2">
                        <label for="Theoretical_limit_known" class="control-label">Theoretical limit</label>
                        </div>

                    <div class="col-md-2">
                       
                        <select  class="form-control form-control-sm custom-select external-comparative-select" id="Theoretical_limit_known">  
                             <option></option>
                            <option value="Available">Available</option>                            
                             <option value="Not Available">Not Available</option>                           
                        </select>
                    </div>
                     <div class="col-md-1">

                             </div>
                             <div class="col-md-1">

                             </div>
                    <div class="col-md-2">
                        <label for="Theoretical_limit_known_Value " class="control-label">Theoretical limit Value </label>
                        </div>

                    <div class="col-md-1">
                       
                       <input class="form-control form-control-sm comparative-value" id="Theoretical_limit_known_Value" autocomplete="off">
                    </div>
                    <div class="col-md-1">
                        <label for="Theoretical_limit_known_Details" class="control-label">Details</label>
                        </div>

                    <div class="col-md-2">
                       <input class="form-control form-control-sm comparative-details" id="Theoretical_limit_known_Details" autocomplete="off">
                    </div>
                    </div>
                    </div>
                     <div class="external-comparative-group">
                    <div class="row g-3 mt-1">
                     <div class="col-md-2">
                        <label for="Statutory_standard_guidline_known" class="control-label">Statutory standard/guideline</label>
                        </div>

                    <div class="col-md-2">
                       
                        <select  class="form-control form-control-sm custom-select external-comparative-select" id="Statutory_standard_guidline_known"> 
                             <option></option>
                            <option value="Available">Available</option>                            
                             <option value="Not Available">Not Available</option>                           
                        </select>
                    </div>
                     <div class="col-md-1">

                             </div>
                             <div class="col-md-1">

                             </div>
                    <div class="col-md-2">
                        <label for="Statutory_standard_guidline_known_Value " class="control-label">Statutory standard Value</label>
                        </div>

                    <div class="col-md-1">
                       
                       <input class="form-control form-control-sm comparative-value" id="Statutory_standard_guidline_known_Value" autocomplete="off">
                    </div>
                    <div class="col-md-1">
                        <label for="Statutory_standard_guidline_known_Details" class="control-label">Details</label>
                        </div>

                    <div class="col-md-2">
                       <input class="form-control form-control-sm comparative-details" id="Statutory_standard_guidline_known_Details" autocomplete="off">
                    </div>
                    </div>
                    </div>
                     <div class="external-comparative-group">
                    <div class="row g-3 mt-1">
                     <div class="col-md-2">
                        <label for="Internal_Benchmark_available" class="control-label">Internal benchmark</label>
                        </div>

                    <div class="col-md-2">
                       
                        <select  class="form-control form-control-sm custom-select external-comparative-select" id="Internal_Benchmark_available">
                             <option></option>
                            <option value="Available">Available</option>                            
                             <option value="Not Available">Not Available</option>                           
                        </select>
                    </div>
                     <div class="col-md-1">

                             </div>
                             <div class="col-md-1">

                             </div>
                    <div class="col-md-2">
                        <label for="Internal_Benchmark_available_Value " class="control-label">Internal benchmark Value</label>
                        </div>

                    <div class="col-md-1">
                       
                       <input class="form-control form-control-sm comparative-value" id="Internal_Benchmark_available_Value" autocomplete="off">
                    </div>
                    <div class="col-md-1">
                        <label for="Internal_Benchmark_available_Details" class="control-label">Details</label>
                        </div>

                    <div class="col-md-2">
                       <input class="form-control form-control-sm comparative-details" id="Internal_Benchmark_available_Details" autocomplete="off">
                    </div>
                    </div>
                    </div>
                     <div class="external-comparative-group">
                    <div class="row g-3 mt-1">
                     <div class="col-md-2">
                        <label for="Historical_bast_available" class="control-label">Historical best</label>
                        </div>

                    <div class="col-md-2">
                       
                        <select  class="form-control form-control-sm custom-select external-comparative-select" id="Historical_bast_available">  
                             <option></option>
                            <option value="Available">Available</option>                            
                             <option value="Not Available">Not Available</option>                           
                        </select>
                    </div>
                     <div class="col-md-1">

                             </div>
                              <div class="col-md-1">

                             </div>
                    <div class="col-md-2">
                        <label for="Historical_bast_available_Value " class="control-label">Historical Value</label>
                        </div>

                    <div class="col-md-1">
                       
                       <input class="form-control form-control-sm comparative-value" id="Historical_bast_available_Value" autocomplete="off">
                    </div>
                    <div class="col-md-1">
                        <label for="Historical_bast_available_Details" class="control-label">Details</label>
                        </div>

                    <div class="col-md-2">
                       <input class="form-control form-control-sm comparative-details" id="Historical_bast_available_Details" autocomplete="off">
                    </div>
                    </div>
                    <div id="periodicityContainer" class="mt-3">
    <label class="form-label fw-bold">Period Targets</label>
    <div id="periodicityFields" class="row gy-2">
        <!-- JS will insert dynamic input fields here -->
    </div>
</div>


</div>
                    <div class="text-center mt-4">
                    @if (ViewBag.CanModify == true || ViewBag.CanWrite == true)
                    {
                        <button class="btn btn-primary me-2 px-4" id="submitButton" type="submit">Submit</button>
                    }
                    @if (ViewBag.CanDelete == true)
                    {
                        <button class="btn btn-danger px-4" id="deleteButton" style="display:none;">Delete</button>
                    }
                </div>
                    </div>
                   

                            </form>

i want to store Data 

        [HttpPost]
        public async Task<IActionResult> TargetKPI(AppKpiMaster model, string actionType)
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

                 
                }


                else
                {
                    if (!canModify)
                        return RedirectToAction("AccessDenied", "TPR");

                   
                }
            }


            else if (actionType == "delete")
            {
                if (!canDelete)
                    return RedirectToAction("AccessDenied", "TPR");

            }

            return BadRequest("Invalid action.");
        }

