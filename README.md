<script>
document.addEventListener('DOMContentLoaded', function () {

    const divisionInput = document.getElementById('divisionDropdown');
    const departmentInput = document.getElementById('departmentDropdown');
    const sectionInput = document.getElementById('sectionDropdown');

    const divisionHidden = document.getElementById('Division');
    const departmentHidden = document.getElementById('Department');
    const sectionHidden = document.getElementById('Section');

    // Handle checkbox changes
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

    // Update visible text and hidden value
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

    // Load Departments by Division
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

    // Load Sections by Department
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




please update according to this 

[HttpGet]
public IActionResult GetDivisions()
{
    using (var connection = new SqlConnection(GetConnection()))
    {
        string query = @"
        SELECT DISTINCT ema_exec_head_desc 
        FROM SAPHRDB.dbo.T_Empl_All
        WHERE ema_exec_head_desc IS NOT NULL
        ORDER BY ema_exec_head_desc";

        var divisions = connection.Query<Division>(query).ToList();
        return Json(divisions);
    }
}

     
[HttpGet]
public IActionResult GetDepartments(string division)
{
    if (string.IsNullOrEmpty(division))
        return Json(new List<Department>());

    using (var connection = new SqlConnection(GetConnection()))
    {
        string query = @"
        SELECT DISTINCT ema_exec_head_desc, ema_dept_desc
        FROM SAPHRDB.dbo.T_Empl_All
        WHERE ema_exec_head_desc = @division
              AND ema_dept_desc IS NOT NULL
        ORDER BY ema_dept_desc";

        var departments = connection.Query<Department>(query, new { division }).ToList();
        return Json(departments);
    }
}

       
[HttpGet]
public IActionResult GetSections(string division, string department)
{
    if (string.IsNullOrEmpty(division) || string.IsNullOrEmpty(department))
        return Json(new List<Section>());

    using (var connection = new SqlConnection(GetConnection()))
    {
        string query = @"
        SELECT DISTINCT ema_exec_head_desc, ema_dept_desc, ema_section_desc
        FROM SAPHRDB.dbo.T_Empl_All
        WHERE ema_exec_head_desc = @division
              AND ema_dept_desc = @department
              AND ema_section_desc IS NOT NULL
        ORDER BY ema_section_desc";

        var sections = connection.Query<Section>(query, new { division, department }).ToList();
        return Json(sections);
    }
}



               <div class="row g-3 mt-1">

  
    <div class="col-md-1">
        <label for="Division" class="control-label">Division</label>
    </div>
    <div class="col-md-3">
        <div class="dropdown">
            <input class="dropdown-toggle form-control form-control-sm custom-select" type="button"
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
            <input class="dropdown-toggle form-control form-control-sm custom-select" type="button"
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
            <input class="dropdown-toggle form-control form-control-sm custom-select" type="button"
                   id="sectionDropdown" data-bs-toggle="dropdown" aria-expanded="false" />
            <ul class="dropdown-menu w-100" aria-labelledby="sectionDropdown" id="sectionList"></ul>
        </div>
        <input type="hidden" id="Section" name="Section" />
    </div>

</div>


<script>
document.addEventListener('DOMContentLoaded', function () {

    const divisionInput = document.getElementById('divisionDropdown');
    const departmentInput = document.getElementById('departmentDropdown');
    const sectionInput = document.getElementById('sectionDropdown');

    const divisionHidden = document.getElementById('Division');
    const departmentHidden = document.getElementById('Department');
    const sectionHidden = document.getElementById('Section');

    // Handle Division checkboxes
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

    // Update visible input & hidden field
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

        const selected = Array.from(checkboxes).filter(cb => cb.checked).map(cb => cb.value);
        hidden.value = selected.join(',');
        input.value = selected.length ? `${selected.length} selected` : '';
    }

    // Load Departments based on selected divisions
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
                                    value="${dept.ema_dept_desc}" id="dept_${dept.ema_dept_desc.replace(/\s+/g, '_')}">
                                <label class="form-check-label" for="dept_${dept.ema_dept_desc.replace(/\s+/g, '_')}">
                                    ${dept.ema_exec_head_desc} - ${dept.ema_dept_desc}
                                </label>
                            </div>
                        </li>`;
                });
            });
        });
    }

    // Load Sections based on selected departments
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
                                    value="${sec.ema_section_desc}" id="sec_${sec.ema_section_desc.replace(/\s+/g, '_')}">
                                <label class="form-check-label" for="sec_${sec.ema_section_desc.replace(/\s+/g, '_')}">
                                    ${sec.ema_exec_head_desc} - ${sec.ema_dept_desc} - ${sec.ema_section_desc}
                                </label>
                            </div>
                        </li>`;
                });
            });
        });
    }

});
</script>
