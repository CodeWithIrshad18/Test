form 
<div id="quarterlyFields" style="display:none;">
   <!-- Q1 -->
  <div class="row g-3 mt-1">
        <div class="col-md-2">
            <label class="control-label">Quarter 1</label>
            </div>
       <div class="col-md-1">
            <label class="control-label">KPI SPOC</label>
           </div>
             <div class="col-md-2">
          
           <input type="datetime-local" class="form-control form-control-sm" name="KPISPOC">
       </div>
            <div class="col-md-1">
                 <label class="control-label">Immediate Superior</label>
           </div>
            <div class="col-md-2">
          
           <input type="datetime-local" class="form-control form-control-sm" name="ImmediateSuperior">
       </div>
            <div class="col-md-1">
                <label class="control-label">HOD</label>
           </div>

       <div class="col-md-2">
           <input type="datetime-local" class="form-control form-control-sm" name="HOD">
       </div>
   </div>

   <!-- Q2 -->
  <div class="row g-3 mt-1">
        <div class="col-md-2">
            <label class="control-label">Quarter 2</label>
            </div>
       <div class="col-md-1">
            <label class="control-label">KPI SPOC</label>
           </div>
             <div class="col-md-2">
          
           <input type="datetime-local" class="form-control form-control-sm" name="KPISPOC">
       </div>
            <div class="col-md-1">
                 <label class="control-label">Immediate Superior</label>
           </div>
            <div class="col-md-2">
          
           <input type="datetime-local" class="form-control form-control-sm" name="ImmediateSuperior">
       </div>
            <div class="col-md-1">
                <label class="control-label">HOD</label>
           </div>

       <div class="col-md-2">
           <input type="datetime-local" class="form-control form-control-sm" name="HOD">
       </div>
   </div>

   <!-- Q3 -->
  <div class="row g-3 mt-1">
        <div class="col-md-2">
            <label class="control-label">Quarter 3</label>
            </div>
       <div class="col-md-1">
            <label class="control-label">KPI SPOC</label>
           </div>
             <div class="col-md-2">
          
           <input type="datetime-local" class="form-control form-control-sm" name="KPISPOC">
       </div>
            <div class="col-md-1">
                 <label class="control-label">Immediate Superior</label>
           </div>
            <div class="col-md-2">
          
           <input type="datetime-local" class="form-control form-control-sm" name="ImmediateSuperior">
       </div>
            <div class="col-md-1">
                <label class="control-label">HOD</label>
           </div>

       <div class="col-md-2">
           <input type="datetime-local" class="form-control form-control-sm" name="HOD">
       </div>
   </div>

   <!-- Q4 -->
   <div class="row g-3 mt-1">
        <div class="col-md-2">
            <label class="control-label">Quarter 4</label>
            </div>
       <div class="col-md-1">
            <label class="control-label">KPI SPOC</label>
           </div>
             <div class="col-md-2">
          
           <input type="datetime-local" class="form-control form-control-sm" name="KPISPOC">
       </div>
            <div class="col-md-1">
                 <label class="control-label">Immediate Superior</label>
           </div>
            <div class="col-md-2">
          
           <input type="datetime-local" class="form-control form-control-sm" name="ImmediateSuperior">
       </div>
            <div class="col-md-1">
                <label class="control-label">HOD</label>
           </div>

       <div class="col-md-2">
           <input type="datetime-local" class="form-control form-control-sm" name="HOD">
       </div>
   </div>


 [HttpPost]
 public async Task<IActionResult> PeriodicityMaster(AppPeriodicityMaster model, string actionType)
 {
     var user = HttpContext.Session.GetString("Session");

     var userIdString = HttpContext.Session.GetString("Session");
     if (string.IsNullOrEmpty(userIdString))
     {
         return RedirectToAction("AccessDenied", "TPR");
     }

     string formName = "PeriodicityMaster";
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
             model.PeriodicityName = model.PeriodicityCode;
             model.Category = model.PeriodicityCode;

             context.AppPeriodicityMasters.Add(model);
         }
         else
         {
             if (!canModify)
             {
                 return RedirectToAction("AccessDenied", "TPR");
             }
             var existingRecord = await context.AppPeriodicityMasters.FindAsync(model.ID);
             if (existingRecord != null)
             {
                 existingRecord.PeriodicityCode = model.PeriodicityCode;
                 existingRecord.PeriodicityName = model.PeriodicityCode;                        
                 existingRecord.CreatedBy = user;
                 context.AppPeriodicityMasters.Update(existingRecord);
             }
             else
             {
                 return NotFound("Record not found.");
             }
         }
         await context.SaveChangesAsync();
         TempData["Success"] = "Periodicity Saved successfully!";
         return RedirectToAction("PeriodicityMaster");
     }
     else if (actionType == "delete")
     {
         if (!canDelete)
         {
             return RedirectToAction("AccessDenied", "TPR");
         }
         var periodicity = await context.AppPeriodicityMasters.FindAsync(model.ID);
         if (periodicity == null)
         {
             return NotFound("Record not found.");
         }

         context.AppPeriodicityMasters.Remove(periodicity);
         await context.SaveChangesAsync();
         TempData["Delete"] = "Periodicity deleted successfully!";
         return RedirectToAction("PeriodicityMaster");
     }

     return BadRequest("Invalid action.");
 }


 <script>
	document.getElementById('form').addEventListener('submit', function (event) {
		event.preventDefault();

		var isValid = true;
		var elements = this.querySelectorAll('input, select, textarea');

		elements.forEach(function (element) {
			if (element.id === 'PeriodicityId'||element.id==='CreatedBy') {
				return;
			}


			if (element.value.trim() === '') {
				isValid = false;
				element.classList.add('is-invalid');
			} else {
				element.classList.remove('is-invalid');
			}
		});


		if (isValid) {
			
				this.submit();
			
		}
	});
</script>

validation like this 
<option>Apr - Jun (Q1 - 1st Qtr)</option>
<option>Jul - Sep (Q2 - 2nd Qtr)</option>
<option>Oct - Dec (Q3 - 3rd Qtr)</option>
<option>Jan - Mar (Q4 - 4th Qtr)</option>
