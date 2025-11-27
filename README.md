controller code: 

 [Authorize(Policy = "CanRead")]
 public async Task<IActionResult> PeriodicityMaster(Guid? id, int page = 1, string searchString = "")
 {
     if (HttpContext.Session.GetString("Session") != null)
     {
         var UserId = HttpContext.Session.GetString("Session");

         ViewBag.user = User;


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
             .Where(p => p.UserId == UserId && p.FormId == form)
             .AnyAsync(p => p.AllowModify == true);

         bool canDelete = await context.AppUserFormPermissions
             .Where(p => p.UserId == UserId && p.FormId == form)
             .AnyAsync(p => p.AllowDelete == true);
         bool canWrite = await context.AppUserFormPermissions
             .Where(p => p.UserId == UserId && p.FormId == form)
             .AnyAsync(p => p.AllowWrite == true);

         ViewBag.CanModify = canModify;
         ViewBag.CanDelete = canDelete;
         ViewBag.CanWrite = canWrite;

         var CreatedOn = DateTime.Now;
         ViewBag.CreatedOn = CreatedOn.ToString("yyyy/MM/dd:HH:mm:ss");

         int pageSize = 5;
         var query = context.AppPeriodicityMasters.AsQueryable();

         if (!string.IsNullOrEmpty(searchString))
         {
             query = query.Where(a => a.PeriodicityCode.Contains(searchString));
         }

         var pagedData = await query.Skip((page - 1) * pageSize).Take(pageSize).ToListAsync();
         var totalCount = query.Count();

         ViewBag.ListData2 = pagedData;
         ViewBag.CurrentPage = page;
         ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
         ViewBag.searchString = searchString;


         AppPeriodicityMaster viewModel = null;

         if (id.HasValue)
         {
             viewModel = await context.AppPeriodicityMasters.FirstOrDefaultAsync(a => a.ID == id);
         }

         return View(viewModel);
     }
     else
     {
         return RedirectToAction("Login", "User");
     }
 }

Js :

<script>

    function togglePeriodicityFields() {
    let code = document.getElementById("PeriodicityCode").value;
    let monthlyYearly = document.getElementById("monthlyYearlyFields");
    let quarterly = document.getElementById("quarterlyFields");

    if (code === "Monthly" || code === "Yearly") {
        monthlyYearly.style.display = "flex";
        quarterly.style.display = "none";
    }
    else if (code === "Quarterly") {
        monthlyYearly.style.display = "none";
        quarterly.style.display = "block";
    }
    else {
        monthlyYearly.style.display = "none";
        quarterly.style.display = "none";
    }
}

document.getElementById("PeriodicityCode")
    .addEventListener("change", togglePeriodicityFields);


    document.addEventListener("DOMContentLoaded", function () {
        var newButton = document.getElementById("newButton");
        var PeriodicityMaster = document.getElementById("form");
        var refNoLinks = document.querySelectorAll(".refNoLink");
        var deleteButton = document.getElementById("deleteButton");
        var submitButton = document.getElementById("submitButton");
        var actionTypeInput = document.getElementById("actionType");

        if (newButton) {
            newButton.addEventListener("click", function () {
                PeriodicityMaster.style.display = "block";
                document.getElementById("PeriodicityCode").value = "";          
                document.getElementById("PeriodicityId").value = "";
                deleteButton.style.display = "none";
            });
        }

        refNoLinks.forEach(link => {
            link.addEventListener("click", function (event) {
                event.preventDefault();
                PeriodicityMaster.style.display = "block";

                document.getElementById("PeriodicityCode").value = this.getAttribute("data-PeriodicityCode");        
                document.getElementById("PeriodicityId").value = this.getAttribute("data-id");

                if (deleteButton) {
                    deleteButton.style.display = "inline-block";
                }
            });
        });

        submitButton.addEventListener("click", function () {
            actionTypeInput.value = "save"; 
        });

        if (deleteButton) {
    deleteButton.addEventListener("click", function () {
        Swal.fire({
            title: 'Are you sure?',
            text: "Do you really want to delete this Periodicity?",
            icon: 'warning',
            showCancelButton: true,
            confirmButtonColor: '#3085d6',
            cancelButtonColor: '#d33',
            confirmButtonText: 'Yes, delete it!',
            cancelButtonText: 'Cancel'
        }).then((result) => {
            if (result.isConfirmed) {
                actionTypeInput.value = "delete";  
                document.getElementById("form").submit();  
            }
        });
    });
}
    });
</script>


grid :
<tbody>
    @if (ViewBag.ListData2 != null)
    {
        @foreach (var item in ViewBag.ListData2)
        {
            <tr>
                <td>
                    <a href="#" class="refNoLink"
                       data-id="@item.ID"
                       data-PeriodicityCode="@item.PeriodicityCode"
                       data-PeriodicityName="@item.PeriodicityName"                                       
                       data-createdBy="@item.CreatedBy">
                        @item.PeriodicityCode
                    </a>
                </td>
            </tr>
        }
    }
    else
    {
        <tr>
            <td colspan="2" class="text-center text-muted py-3">No data available</td>
        </tr>
    }
</tbody>

form :
                <div class="row g-3">
                    <input type="hidden" asp-for="ID" id="PeriodicityId" name="ID" />
                    <input type="hidden" id="actionType" name="actionType" />

                    <div class="col-md-3">
                        <label for="PeriodicityCode" class="control-label">Periodicity Code</label>                    

   <Select asp-for="PeriodicityCode" class="form-control form-control-sm custom-select" id="PeriodicityCode"  type="text">
        <option value=""></option>   
    <option value="Monthly">Monthly</option>
    <option value="Yearly">Yearly</option>
    <option value="Quarterly">Quarterly</option>
</Select>
                 </div>
 
       <div id="monthlyYearlyFields" class="row g-3 mt-2" style="display:none;">
    <div class="col-md-3">
        <label class="control-label" for="KpiSPOC">KPI SPOC Date</label>
        <input type="datetime-local" class="form-control form-control-sm" id="KpiSPOC" name="KPISPOC">
    </div>

    <div class="col-md-3">
        <label class="control-label" for="ImmediateSuperior">Immediate Superior Date</label>
        <input type="datetime-local" class="form-control form-control-sm" id="ImmediateSuperior" name="ImmediateSuperior">
    </div>

    <div class="col-md-3">
        <label class="control-label" for="HOD">HOD Date</label>
        <input type="datetime-local" class="form-control form-control-sm" id="HOD" name="HOD">
    </div>



 </div>
 <div id="quarterlyFields" style="display:none;">
    <!-- Q1 -->
   <div class="row g-3 mt-1">
         <div class="col-md-2">
             <label class="control-label">Quarter 1</label>
             </div>
        <div class="col-md-1">
             <label class="control-label" for="Q1_KPISPOC">KPI SPOC</label>
            </div>
              <div class="col-md-2">
           
            <input type="datetime-local" class="form-control form-control-sm" name="Q1_KPISPOC" id="Q1_KPISPOC">
            <span class="text-danger small error-msg" data-for="Q1_KPISPOC"></span>
        </div>
             <div class="col-md-1">
                  <label class="control-label" for="Q1_ImmediateSuperior">Immediate Superior</label>
            </div>
             <div class="col-md-2">
           
            <input type="datetime-local" class="form-control form-control-sm" name="Q1_ImmediateSuperior" id="Q1_ImmediateSuperior">
            <span class="text-danger small error-msg" data-for="Q1_ImmediateSuperior"></span>
        </div>
             <div class="col-md-1">
                 <label class="control-label" for="Q1_HOD">HOD</label>
            </div>

        <div class="col-md-2">
            <input type="datetime-local" class="form-control form-control-sm" name="Q1_HOD" id="Q1_HOD">
            <span class="text-danger small error-msg" data-for="Q1_HOD"></span>
        </div>
    </div>

    <!-- Q2 -->
   <div class="row g-3 mt-1">
         <div class="col-md-2">
             <label class="control-label">Quarter 2</label>
             </div>
        <div class="col-md-1">
             <label class="control-label" for="Q2_KPISPOC">KPI SPOC</label>
            </div>
              <div class="col-md-2">
           
            <input type="datetime-local" class="form-control form-control-sm" name="Q2_KPISPOC" id="Q2_KPISPOC">
            <span class="text-danger small error-msg" data-for="Q2_KPISPOC"></span>
        </div>
             <div class="col-md-1">
                  <label class="control-label" for="Q2_ImmediateSuperior">Immediate Superior</label>
            </div>
             <div class="col-md-2">
           
            <input type="datetime-local" class="form-control form-control-sm" name="Q2_ImmediateSuperior" id="Q2_ImmediateSuperior">
            <span class="text-danger small error-msg" data-for="Q2_ImmediateSuperior"></span>
        </div>
             <div class="col-md-1">
                 <label class="control-label" for="Q2_HOD">HOD</label>
            </div>

        <div class="col-md-2">
            <input type="datetime-local" class="form-control form-control-sm" name="Q2_HOD" id="Q2_HOD">
            <span class="text-danger small error-msg" data-for="Q2_HOD"></span>
        </div>
    </div>

    <!-- Q3 -->
   <div class="row g-3 mt-1">
         <div class="col-md-2">
             <label class="control-label">Quarter 3</label>
             </div>
        <div class="col-md-1">
             <label class="control-label" for="Q3_KPISPOC">KPI SPOC</label>
            </div>
              <div class="col-md-2">
           
            <input type="datetime-local" class="form-control form-control-sm" name="Q3_KPISPOC" id="Q3_KPISPOC">
            <span class="text-danger small error-msg" data-for="Q3_KPISPOC"></span>
        </div>
             <div class="col-md-1">
                  <label class="control-label" for="Q3_ImmediateSuperior">Immediate Superior</label>
            </div>
             <div class="col-md-2">
           
            <input type="datetime-local" class="form-control form-control-sm" name="Q3_ImmediateSuperior" id="Q3_ImmediateSuperior">
            <span class="text-danger small error-msg" data-for="Q3_ImmediateSuperior"></span>
        </div>
             <div class="col-md-1">
                 <label class="control-label" for="Q3_HOD">HOD</label>
            </div>

        <div class="col-md-2">
            <input type="datetime-local" class="form-control form-control-sm" name="Q3_HOD" id="Q3_HOD">
            <span class="text-danger small error-msg" data-for="Q3_HOD"></span>
        </div>
    </div>

    <!-- Q4 -->
    <div class="row g-3 mt-1">
         <div class="col-md-2">
             <label class="control-label">Quarter 4</label>
             </div>
        <div class="col-md-1">
             <label class="control-label" for="Q4_KPISPOC">KPI SPOC</label>
            </div>
              <div class="col-md-2">
           
            <input type="datetime-local" class="form-control form-control-sm" name="Q4_KPISPOC" id="Q4_KPISPOC">
            <span class="text-danger small error-msg" data-for="Q4_KPISPOC"></span>
        </div>
             <div class="col-md-1">
                  <label class="control-label" for="Q4_ImmediateSuperior">Immediate Superior</label>
            </div>
             <div class="col-md-2">
           
            <input type="datetime-local" class="form-control form-control-sm" name="Q4_ImmediateSuperior" id="Q4_ImmediateSuperior">
            <span class="text-danger small error-msg" data-for="Q4_ImmediateSuperior"></span>
        </div>
             <div class="col-md-1">
                 <label class="control-label" for="Q4_HOD">HOD</label>
            </div>

        <div class="col-md-2">
            <input type="datetime-local" class="form-control form-control-sm" name="Q4_HOD" id="Q4_HOD">
            <span class="text-danger small error-msg" data-for="Q4_HOD"></span>
        </div>
    </div>
</div>


i want to existing data to populate on the fields when click on RefNoLink and for quarter there are 4 rows in the table but show one and when click on RefnoLink then open four quarter with values 
