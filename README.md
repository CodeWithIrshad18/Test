<script>
document.addEventListener("DOMContentLoaded", function () {

    function updateHiddenInput(listSelector, hiddenInputId) {
        const checkboxes = document.querySelectorAll(`${listSelector} input[type="checkbox"]:checked`);
        const values = Array.from(checkboxes).map(cb => cb.value);
        document.getElementById(hiddenInputId).value = values.join(":");
    }

    // When any Division checkbox is clicked
    document.querySelectorAll("#divisionList input[type='checkbox']").forEach(cb => {
        cb.addEventListener("change", function () {
            updateHiddenInput("#divisionList", "Division");
        });
    });

    // When any Department checkbox is clicked
    document.querySelectorAll("#departmentList input[type='checkbox']").forEach(cb => {
        cb.addEventListener("change", function () {
            updateHiddenInput("#departmentList", "Department");
        });
    });

    // When any Section checkbox is clicked
    document.querySelectorAll("#sectionList input[type='checkbox']").forEach(cb => {
        cb.addEventListener("change", function () {
            updateHiddenInput("#sectionList", "Section");
        });
    });
});
</script>




I have this post method
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
these are my three dropdown

    <div class="col-md-1">
        <label for="Division" class="control-label">Division</label>
    </div>
    <div class="col-md-3">
        <div class="dropdown">
            <input class="dropdown-toggle form-control form-control-sm custom-select text-start" type="button"
                   id="divisionDropdown" data-bs-toggle="dropdown" aria-expanded="false"/>
            <ul class="dropdown-menu w-100" aria-labelledby="divisionDropdown" id="divisionList">
                @foreach (var item in ViewBag.DivisionDropdown as List<Division>)
                {
                    <li style="margin-left:5%;">
                        <div class="form-check">
                            <input type="checkbox" class="form-check-input division-checkbox"
                                   value="@item.ema_exec_head_desc" id="div_@item.ema_exec_head_desc" />
                            <label class="form-check-label" for="div_@item.ema_exec_head_desc">
                                @item.ema_exec_head_desc
                            </label>
                        </div>
                    </li>
                }
            </ul>
        </div>
        <input type="hidden" id="Division" name="Division" />
    </div>

    <div class="col-md-1">
        <label for="Department" class="control-label">Department</label>
    </div>
    <div class="col-md-3">
        <div class="dropdown">
            <input class="dropdown-toggle form-control form-control-sm custom-select text-start" type="button"
                   id="departmentDropdown" data-bs-toggle="dropdown" aria-expanded="false"/>
            <ul class="dropdown-menu w-100" aria-labelledby="departmentDropdown" id="departmentList"></ul>
        </div>
        <input type="hidden" id="Department" name="Department" />
    </div>

 
    <div class="col-md-1">
        <label for="Section" class="control-label">Section</label>
    </div>
    <div class="col-md-3">
        <div class="dropdown">
            <input class="dropdown-toggle form-control form-control-sm custom-select text-start" type="button"
                   id="sectionDropdown" data-bs-toggle="dropdown" aria-expanded="false" />
            <ul class="dropdown-menu w-100" aria-labelledby="sectionDropdown" id="sectionList"></ul>
        </div>
        <input type="hidden" id="Section" name="Section" />
    </div>

</div>
