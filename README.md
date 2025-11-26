<input type="datetime-local" name="Q1_KPISPOC">
<input type="datetime-local" name="Q1_Superior">
<input type="datetime-local" name="Q1_HOD">

<input type="datetime-local" name="Q2_KPISPOC">
<input type="datetime-local" name="Q2_Superior">
<input type="datetime-local" name="Q2_HOD">

<input type="datetime-local" name="Q3_KPISPOC">
<input type="datetime-local" name="Q3_Superior">
<input type="datetime-local" name="Q3_HOD">

<input type="datetime-local" name="Q4_KPISPOC">
<input type="datetime-local" name="Q4_Superior">
<input type="datetime-local" name="Q4_HOD">

if (actionType == "save")
{
    if (!canWrite)
        return RedirectToAction("AccessDenied", "TPR");

    // Retrieve quarter values manually
    var Q1_KPISPOC = Request.Form["Q1_KPISPOC"].ToString();
    var Q1_Superior = Request.Form["Q1_Superior"].ToString();
    var Q1_HOD = Request.Form["Q1_HOD"].ToString();

    var Q2_KPISPOC = Request.Form["Q2_KPISPOC"].ToString();
    var Q2_Superior = Request.Form["Q2_Superior"].ToString();
    var Q2_HOD = Request.Form["Q2_HOD"].ToString();

    var Q3_KPISPOC = Request.Form["Q3_KPISPOC"].ToString();
    var Q3_Superior = Request.Form["Q3_Superior"].ToString();
    var Q3_HOD = Request.Form["Q3_HOD"].ToString();

    var Q4_KPISPOC = Request.Form["Q4_KPISPOC"].ToString();
    var Q4_Superior = Request.Form["Q4_Superior"].ToString();
    var Q4_HOD = Request.Form["Q4_HOD"].ToString();


    // ***********************
    // MONTHLY / YEARLY
    // ***********************
    if (model.PeriodicityCode == "Monthly" || model.PeriodicityCode == "Yearly")
    {
        model.CreatedBy = user;
        model.PeriodicityName = model.PeriodicityCode;
        model.Category = model.PeriodicityCode;

        context.AppPeriodicityMasters.Add(model);
        await context.SaveChangesAsync();
        TempData["Success"] = "Periodicity Saved successfully!";
        return RedirectToAction("PeriodicityMaster");
    }


    // ***********************
    // QUARTERLY SAVE 4 ROWS
    // ***********************
    if (model.PeriodicityCode == "Quaterly")
    {
        List<AppPeriodicityMaster> quarters = new List<AppPeriodicityMaster>();

        quarters.Add(new AppPeriodicityMaster {
            ID = Guid.NewGuid(),
            PeriodicityCode = "Quaterly",
            PeriodicityName = "Apr - Jun (Q1 - 1st Qtr)",
            KPISPOC = string.IsNullOrEmpty(Q1_KPISPOC) ? null : DateTime.Parse(Q1_KPISPOC),
            ImmediateSuperior = string.IsNullOrEmpty(Q1_Superior) ? null : DateTime.Parse(Q1_Superior),
            HOD = string.IsNullOrEmpty(Q1_HOD) ? null : DateTime.Parse(Q1_HOD),
            CreatedBy = user,
            Category = "Quaterly",
            CreatedOn = DateTime.Now
        });

        quarters.Add(new AppPeriodicityMaster {
            ID = Guid.NewGuid(),
            PeriodicityCode = "Quaterly",
            PeriodicityName = "Jul - Sep (Q2 - 2nd Qtr)",
            KPISPOC = string.IsNullOrEmpty(Q2_KPISPOC) ? null : DateTime.Parse(Q2_KPISPOC),
            ImmediateSuperior = string.IsNullOrEmpty(Q2_Superior) ? null : DateTime.Parse(Q2_Superior),
            HOD = string.IsNullOrEmpty(Q2_HOD) ? null : DateTime.Parse(Q2_HOD),
            CreatedBy = user,
            Category = "Quaterly",
            CreatedOn = DateTime.Now
        });

        quarters.Add(new AppPeriodicityMaster {
            ID = Guid.NewGuid(),
            PeriodicityCode = "Quaterly",
            PeriodicityName = "Oct - Dec (Q3 - 3rd Qtr)",
            KPISPOC = string.IsNullOrEmpty(Q3_KPISPOC) ? null : DateTime.Parse(Q3_KPISPOC),
            ImmediateSuperior = string.IsNullOrEmpty(Q3_Superior) ? null : DateTime.Parse(Q3_Superior),
            HOD = string.IsNullOrEmpty(Q3_HOD) ? null : DateTime.Parse(Q3_HOD),
            CreatedBy = user,
            Category = "Quaterly",
            CreatedOn = DateTime.Now
        });

        quarters.Add(new AppPeriodicityMaster {
            ID = Guid.NewGuid(),
            PeriodicityCode = "Quaterly",
            PeriodicityName = "Jan - Mar (Q4 - 4th Qtr)",
            KPISPOC = string.IsNullOrEmpty(Q4_KPISPOC) ? null : DateTime.Parse(Q4_KPISPOC),
            ImmediateSuperior = string.IsNullOrEmpty(Q4_Superior) ? null : DateTime.Parse(Q4_Superior),
            HOD = string.IsNullOrEmpty(Q4_HOD) ? null : DateTime.Parse(Q4_HOD),
            CreatedBy = user,
            Category = "Quaterly",
            CreatedOn = DateTime.Now
        });

        context.AppPeriodicityMasters.AddRange(quarters);
        await context.SaveChangesAsync();

        TempData["Success"] = "Quarterly Periodicity saved successfully!";
        return RedirectToAction("PeriodicityMaster");
    }
}

function validateQuarterDate(name, min, max) {
    let input = document.querySelector(`[name="${name}"]`);
    if (!input || input.value === "") return true;

    let date = new Date(input.value);
    let minDate = new Date(min);
    let maxDate = new Date(max);

    if (date < minDate || date > maxDate) {
        input.classList.add("is-invalid");
        return false;
    }

    input.classList.remove("is-invalid");
    return true;
}

document.getElementById('form').addEventListener('submit', function (event) {

    let valid =
        validateQuarterDate("Q1_KPISPOC", "2025-04-01", "2025-06-30") &&
        validateQuarterDate("Q1_Superior", "2025-04-01", "2025-06-30") &&
        validateQuarterDate("Q1_HOD", "2025-04-01", "2025-06-30") &&

        validateQuarterDate("Q2_KPISPOC", "2025-07-01", "2025-09-30") &&
        validateQuarterDate("Q2_Superior", "2025-07-01", "2025-09-30") &&
        validateQuarterDate("Q2_HOD", "2025-07-01", "2025-09-30") &&

        validateQuarterDate("Q3_KPISPOC", "2025-10-01", "2025-12-31") &&
        validateQuarterDate("Q3_Superior", "2025-10-01", "2025-12-31") &&
        validateQuarterDate("Q3_HOD", "2025-10-01", "2025-12-31") &&

        validateQuarterDate("Q4_KPISPOC", "2025-01-01", "2025-03-31") &&
        validateQuarterDate("Q4_Superior", "2025-01-01", "2025-03-31") &&
        validateQuarterDate("Q4_HOD", "2025-01-01", "2025-03-31");

    if (!valid) {
        event.preventDefault();
        alert("Quarter date values must be within allowed range.");
    }
});




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
