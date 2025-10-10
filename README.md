<div class="row">

    <!-- Division -->
    <div class="col-sm-3">
        <label><b>Division</b></label>
        <div class="dropdown">
            <input class="dropdown-toggle form-control form-control-sm custom-select" type="button"
                   id="divisionDropdown" data-bs-toggle="dropdown" aria-expanded="false" readonly />

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

    <!-- Department -->
    <div class="col-sm-3">
        <label><b>Department</b></label>
        <div class="dropdown">
            <input class="dropdown-toggle form-control form-control-sm custom-select" type="button"
                   id="departmentDropdown" data-bs-toggle="dropdown" aria-expanded="false" readonly />
            <ul class="dropdown-menu w-100" aria-labelledby="departmentDropdown" id="departmentList"></ul>
        </div>
        <input type="hidden" id="Department" name="Department" />
    </div>

    <!-- Section -->
    <div class="col-sm-3">
        <label><b>Section</b></label>
        <div class="dropdown">
            <input class="dropdown-toggle form-control form-control-sm custom-select" type="button"
                   id="sectionDropdown" data-bs-toggle="dropdown" aria-expanded="false" readonly />
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
            $.getJSON('/YourController/GetDepartments', { division: division }, function (data) {
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
            $.getJSON('/YourController/GetSections', { division: division, department: dept }, function (data) {
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

.dropdown-menu {
    max-height: 300px;
    overflow-y: auto;
}
.dropdown-menu .form-check-label {
    font-size: 14px;
}



previously I have used this 

<div class="col-sm-3">
    <div class="dropdown">
        <input class="dropdown-toggle form-control form-control-sm custom-select" placeholder="" type="button"
               id="worksiteDropdown" data-bs-toggle="dropdown" aria-expanded="false" />



        <ul class="dropdown-menu w-100" aria-labelledby="worksiteDropdown" id="locationList">
            @foreach (var item in WorksiteDropdown)
            {
                <li style="margin-left:5%;">
                    <div class="form-check">
                        <input type="checkbox" class="form-check-input worksite-checkbox"
                               value="@item.Value" id="worksite_@item.Value" />
                        <label class="form-check-label" for="worksite_@item.Value">@item.Text</label>
                    </div>

                </li>
            }
        </ul>

    </div>

<script>
    document.addEventListener('DOMContentLoaded', function () {
        const DropdownInput = document.getElementById('worksiteDropdown');
        const checkboxes = document.querySelectorAll('.worksite-checkbox');

        const hiddenInput = document.getElementById('Worksite');


        checkboxes.forEach(checkbox => {
            checkbox.addEventListener('change', () => {
                const selectedValues = Array.from(checkboxes).filter(cb => cb.checked).map(cb => cb.value);
                hiddenInput.value = selectedValues.join(',');
            });

        });

        function UpdateSelectedCount() {
            const selectedCount = Array.from(checkboxes).filter(checkbox => checkbox.checked).length;
            DropdownInput.value = `${selectedCount} selected`;
        }

        checkboxes.forEach(checkbox => {
            checkbox.addEventListener('change', UpdateSelectedCount);
        });


    });




</script>

I want like this 
