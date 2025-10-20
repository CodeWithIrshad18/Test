this is my view model 

 public class TargetViewModel
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
     public Guid MasterID { get; set; }
     public string? PeriodicityTransactionID { get; set; }
     public string? TargetValue { get; set; }
     public DateTime CreatedOn { get; set; }


 }

and this Is my Js for 

 groups.forEach(group => {
     const dropdown = group.querySelector('.external-comparative-select');
     const valueInput = group.querySelector('.comparative-value');
     const detailsInput = group.querySelector('.comparative-details');

    

     dropdown.addEventListener('change', function () {
         if (this.value === 'No') {
             valueInput.readOnly = true;
             detailsInput.readOnly = true;

             valueInput.value = '';
             detailsInput.value = '';

             valueInput.classList.remove('is-invalid');
             detailsInput.classList.remove('is-invalid');
         } else {
             valueInput.readOnly = false;
             detailsInput.readOnly = false;
         }
     });
 });

and this is my controller 


 [HttpPost]
 public async Task<IActionResult> TargetKPI(AppTarget model, string actionType, List<AppTargetDetails>? targetDetails)
  {
     var userId = HttpContext.Session.GetString("Session");
     if (string.IsNullOrEmpty(userId))
         return RedirectToAction("AccessDenied", "TPR");

     string formName = "TargetKPI";
     var form = await context.AppFormDetails
         .Where(f => f.FormName == formName)
         .Select(f => f.Id)
         .FirstOrDefaultAsync();

     if (form == default)
         return RedirectToAction("AccessDenied", "TPR");

     bool canModify = await context.AppUserFormPermissions
             .Where(p => p.UserId == userId && p.FormId == form)
             .AnyAsync(p => p.AllowModify == true);
     bool canDelete = await context.AppUserFormPermissions
         .Where(p => p.UserId == userId && p.FormId == form)
         .AnyAsync(p => p.AllowDelete == true);
     bool canWrite = await context.AppUserFormPermissions
         .Where(p => p.UserId == userId && p.FormId == form)
         .AnyAsync(p => p.AllowWrite == true);

     if (actionType == "save")
     {
        
           
             if (!canWrite)
                 return RedirectToAction("AccessDenied", "TPR");

             model.ID = Guid.NewGuid();
             model.CreatedBy = userId;
             model.KPIID = model.KPIID;

             await context.AppTargets.AddAsync(model);
       
         if (targetDetails != null && targetDetails.Any())
         {
          
             var existingDetails = context.AppTargetDetails.Where(d => d.MasterID == model.ID);
             context.AppTargetDetails.RemoveRange(existingDetails);

            
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


target data is not inserting 
