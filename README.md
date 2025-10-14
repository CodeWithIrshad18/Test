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

this is my js 

<script>
document.addEventListener('DOMContentLoaded', function () {

    const divisionInput = document.getElementById('divisionDropdown');
    const departmentInput = document.getElementById('departmentDropdown');
    const sectionInput = document.getElementById('sectionDropdown');

    const divisionHidden = document.getElementById('Division');
    const departmentHidden = document.getElementById('Department');
    const sectionHidden = document.getElementById('Section');

  
    document.addEventListener('change', function (e) {
        if (e.target.classList.contains('division-checkbox')) {
            updateSelection('division');
            loadDepartments();
        }
        if (e.target.classList.contains('department-checkbox')) {
            updateSelection('department');
            loadSections();
        }
        if (e.target.classList.contains('section-checkbox')) {
            updateSelection('section');
        }
    });

  
    function updateSelection(type) {
        let checkboxes, input, hidden;
        if (type === 'division') {
            checkboxes = document.querySelectorAll('.division-checkbox');
            input = divisionInput;
            hidden = divisionHidden;
        } else if (type === 'department') {
            checkboxes = document.querySelectorAll('.department-checkbox');
            input = departmentInput;
            hidden = departmentHidden;
        } else {
            checkboxes = document.querySelectorAll('.section-checkbox');
            input = sectionInput;
            hidden = sectionHidden;
        }

        const selected = Array.from(checkboxes)
            .filter(cb => cb.checked)
            .map(cb => cb.value);

        hidden.value = selected.join(',');
        input.value = selected.length ? `${selected.length} selected` : '';
    }

    function loadDepartments() {
        const selectedDivisions = divisionHidden.value.split(',').filter(x => x);
        const deptList = document.getElementById('departmentList');
        const secList = document.getElementById('sectionList');
        deptList.innerHTML = '';
        secList.innerHTML = '';
        departmentInput.value = '';
        sectionInput.value = '';

        selectedDivisions.forEach(division => {
            $.getJSON('/TPR/GetDepartments', { division: division }, function (data) {
                data.forEach(dept => {
                    deptList.innerHTML += `
                        <li style="margin-left:5%;">
                            <div class="form-check">
                                <input type="checkbox" class="form-check-input department-checkbox"
                                    data-division="${dept.ema_exec_head_desc}"
                                    value="${dept.ema_dept_desc}"
                                    id="dept_${dept.ema_dept_desc.replace(/\s+/g, '_')}">
                                <label class="form-check-label" for="dept_${dept.ema_dept_desc.replace(/\s+/g, '_')}">
                                    ${dept.ema_dept_desc}
                                </label>
                            </div>
                        </li>`;
                });
            });
        });
    }

    function loadSections() {
        const selectedDepts = Array.from(document.querySelectorAll('.department-checkbox:checked'));
        const secList = document.getElementById('sectionList');
        secList.innerHTML = '';
        sectionInput.value = '';

        selectedDepts.forEach(cb => {
            const division = cb.getAttribute('data-division');
            const dept = cb.value;

            $.getJSON('/TPR/GetSections', { division: division, department: dept }, function (data) {
                data.forEach(sec => {
                    secList.innerHTML += `
                        <li style="margin-left:5%;">
                            <div class="form-check">
                                <input type="checkbox" class="form-check-input section-checkbox"
                                    value="${sec.ema_section_desc}"
                                    id="sec_${sec.ema_section_desc.replace(/\s+/g, '_')}">
                                <label class="form-check-label" for="sec_${sec.ema_section_desc.replace(/\s+/g, '_')}">
                                    ${sec.ema_section_desc}
                                </label>
                            </div>
                        </li>`;
                });
            });
        });
    }

});
</script>

and this is my controller method 

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

i want to store division , department and section as semi colon separated for multiple
