<script>
document.addEventListener('DOMContentLoaded', function () {
    const divisionInput = document.getElementById('divisionDropdown');
    const departmentInput = document.getElementById('departmentDropdown');
    const sectionInput = document.getElementById('sectionDropdown');
    const divisionHidden = document.getElementById('Division');
    const departmentHidden = document.getElementById('Department');
    const sectionHidden = document.getElementById('Section');
    const deptList = document.getElementById('departmentList');
    const secList = document.getElementById('sectionList');

    const newButton = document.getElementById("newButton");
    const refNoLinks = document.querySelectorAll(".refNoLink");
    const deleteButton = document.getElementById("deleteButton");
    const submitButton = document.getElementById("submitButton");
    const KPIMaster = document.getElementById("form");
    const actionTypeInput = document.getElementById("actionType");

    // ---------------------------------------------------------------------
    // ✅ Handle "New" Button - reset everything cleanly
    // ---------------------------------------------------------------------
    if (newButton) {
        newButton.addEventListener("click", function () {
            KPIMaster.style.display = "block";
            [
                "KPICode", "KPILevel", "Company", "PerspectiveID", "UnitID",
                "KPIDefination", "KPIDetails", "PeriodicityID",
                "GoodPerformance", "NoofDecimal", "TypeofKPIID", "KPIID"
            ].forEach(id => {
                const el = document.getElementById(id);
                if (el) el.value = "";
            });

            document.querySelectorAll('.division-checkbox, .department-checkbox, .section-checkbox')
                .forEach(cb => cb.checked = false);

            divisionInput.value = departmentInput.value = sectionInput.value = '';
            divisionHidden.value = departmentHidden.value = sectionHidden.value = '';
            deptList.innerHTML = '';
            secList.innerHTML = '';

            if (deleteButton) deleteButton.style.display = "none";
        });
    }

    // ---------------------------------------------------------------------
    // ✅ Handle Checkboxes dynamically
    // ---------------------------------------------------------------------
    document.addEventListener('change', function (e) {
        if (e.target.classList.contains('division-checkbox')) {
            handleDivisionChange(e.target);
        } else if (e.target.classList.contains('department-checkbox')) {
            handleDepartmentChange(e.target);
        } else if (e.target.classList.contains('section-checkbox')) {
            updateHiddenFields();
        }
    });

    // ---------------------------------------------------------------------
    // ✅ Division change → load or remove related Departments
    // ---------------------------------------------------------------------
    function handleDivisionChange(checkbox) {
        const division = checkbox.value;

        if (checkbox.checked) {
            // Load departments for this division
            $.getJSON('/TPR/GetDepartments', { division: division }, function (data) {
                data.forEach(dept => {
                    const existing = document.querySelector(`#dept_${CSS.escape(dept.ema_dept_desc)}`);
                    if (!existing) {
                        deptList.innerHTML += `
                            <li data-division="${division}">
                                <div class="form-check" style="margin-left:5%;">
                                    <input type="checkbox" class="form-check-input department-checkbox"
                                           data-division="${division}"
                                           value="${dept.ema_dept_desc}"
                                           id="dept_${dept.ema_dept_desc}">
                                    <label class="form-check-label" for="dept_${dept.ema_dept_desc}">
                                        ${dept.ema_dept_desc}
                                    </label>
                                </div>
                            </li>`;
                    }
                });
            });
        } else {
            // Remove all departments and sections tied to this division
            deptList.querySelectorAll(`li[data-division="${division}"]`).forEach(li => li.remove());
            secList.querySelectorAll(`li[data-parent-division="${division}"]`).forEach(li => li.remove());
        }

        updateHiddenFields();
    }

    // ---------------------------------------------------------------------
    // ✅ Department change → load or remove related Sections
    // ---------------------------------------------------------------------
    function handleDepartmentChange(checkbox) {
        const division = checkbox.getAttribute('data-division');
        const department = checkbox.value;

        if (checkbox.checked) {
            $.getJSON('/TPR/GetSections', { division: division, department: department }, function (data) {
                data.forEach(sec => {
                    const existing = document.querySelector(`#sec_${CSS.escape(sec.ema_section_desc)}`);
                    if (!existing) {
                        secList.innerHTML += `
                            <li data-department="${department}" data-parent-division="${division}">
                                <div class="form-check" style="margin-left:5%;">
                                    <input type="checkbox" class="form-check-input section-checkbox"
                                           data-division="${division}"
                                           data-department="${department}"
                                           value="${sec.ema_section_desc}"
                                           id="sec_${sec.ema_section_desc}">
                                    <label class="form-check-label" for="sec_${sec.ema_section_desc}">
                                        ${sec.ema_section_desc}
                                    </label>
                                </div>
                            </li>`;
                    }
                });
            });
        } else {
            // Unchecked department → remove its sections
            secList.querySelectorAll(`li[data-department="${department}"]`).forEach(li => li.remove());
        }

        updateHiddenFields();
    }

    // ---------------------------------------------------------------------
    // ✅ Update hidden fields and dropdown display text
    // ---------------------------------------------------------------------
    function updateHiddenFields() {
        const selectedDivisions = Array.from(document.querySelectorAll('.division-checkbox:checked')).map(cb => cb.value);
        const selectedDepartments = Array.from(document.querySelectorAll('.department-checkbox:checked')).map(cb => cb.value);
        const selectedSections = Array.from(document.querySelectorAll('.section-checkbox:checked')).map(cb => cb.value);

        divisionHidden.value = selectedDivisions.join(';');
        departmentHidden.value = selectedDepartments.join(';');
        sectionHidden.value = selectedSections.join(';');

        divisionInput.value = selectedDivisions.length ? `${selectedDivisions.length} selected` : '';
        departmentInput.value = selectedDepartments.length ? `${selectedDepartments.length} selected` : '';
        sectionInput.value = selectedSections.length ? `${selectedSections.length} selected` : '';
    }

    // ---------------------------------------------------------------------
    // ✅ RefNoLink (Edit Mode) – Load saved data
    // ---------------------------------------------------------------------
    refNoLinks.forEach(link => {
        link.addEventListener("click", function (event) {
            event.preventDefault();
            KPIMaster.style.display = "block";

            const divisions = this.getAttribute("data-Division")?.split(';').map(v => v.trim()) || [];
            const departments = this.getAttribute("data-Department")?.split(';').map(v => v.trim()) || [];
            const sections = this.getAttribute("data-Section")?.split(';').map(v => v.trim()) || [];

            document.querySelectorAll('.division-checkbox').forEach(cb => {
                cb.checked = divisions.includes(cb.value.trim());
            });

            divisionHidden.value = divisions.join(';');
            divisionInput.value = divisions.length ? `${divisions.length} selected` : '';

            // Load departments sequentially
            loadDepartmentsFor(divisions, departments, sections);
        });
    });

    function loadDepartmentsFor(divisions, departments, sections) {
        deptList.innerHTML = '';
        secList.innerHTML = '';

        let requests = divisions.length;
        if (requests === 0) return;

        divisions.forEach(division => {
            $.getJSON('/TPR/GetDepartments', { division: division }, function (data) {
                data.forEach(dept => {
                    const checked = departments.includes(dept.ema_dept_desc) ? 'checked' : '';
                    deptList.innerHTML += `
                        <li data-division="${division}">
                            <div class="form-check" style="margin-left:5%;">
                                <input type="checkbox" class="form-check-input department-checkbox"
                                       data-division="${division}"
                                       value="${dept.ema_dept_desc}"
                                       id="dept_${dept.ema_dept_desc}" ${checked}>
                                <label class="form-check-label" for="dept_${dept.ema_dept_desc}">
                                    ${dept.ema_dept_desc}
                                </label>
                            </div>
                        </li>`;
                });
            }).always(() => {
                requests--;
                if (requests === 0) loadSectionsFor(divisions, departments, sections);
            });
        });
    }

    function loadSectionsFor(divisions, departments, sections) {
        let deptRequests = departments.length;
        if (deptRequests === 0) return;

        departments.forEach(dept => {
            const division = divisions.find(d => true);
            $.getJSON('/TPR/GetSections', { division: division, department: dept }, function (data) {
                data.forEach(sec => {
                    const checked = sections.includes(sec.ema_section_desc) ? 'checked' : '';
                    secList.innerHTML += `
                        <li data-department="${dept}" data-parent-division="${division}">
                            <div class="form-check" style="margin-left:5%;">
                                <input type="checkbox" class="form-check-input section-checkbox"
                                       data-division="${division}"
                                       data-department="${dept}"
                                       value="${sec.ema_section_desc}"
                                       id="sec_${sec.ema_section_desc}" ${checked}>
                                <label class="form-check-label" for="sec_${sec.ema_section_desc}">
                                    ${sec.ema_section_desc}
                                </label>
                            </div>
                        </li>`;
                });
            });
        });

        updateHiddenFields();
    }
});
</script>





these
               <div class="row g-3 mt-1">
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


<script>
document.addEventListener('DOMContentLoaded', function () {

  
    const newButton = document.getElementById("newButton");
    const KPIMaster = document.getElementById("form");
    const refNoLinks = document.querySelectorAll(".refNoLink");
    const deleteButton = document.getElementById("deleteButton");
    const submitButton = document.getElementById("submitButton");
    const actionTypeInput = document.getElementById("actionType");

    const divisionInput = document.getElementById('divisionDropdown');
    const departmentInput = document.getElementById('departmentDropdown');
    const sectionInput = document.getElementById('sectionDropdown');

    const divisionHidden = document.getElementById('Division');
    const departmentHidden = document.getElementById('Department');
    const sectionHidden = document.getElementById('Section');

    const deptList = document.getElementById('departmentList');
    const secList = document.getElementById('sectionList');

    if (newButton) {
        newButton.addEventListener("click", function () {
            KPIMaster.style.display = "block";

            [
                "KPICode", "KPILevel", "Company", "Division", "Department", "Section",
                "PerspectiveID", "UnitID", "KPIDefination", "KPIDetails", "PeriodicityID",
                "GoodPerformance", "NoofDecimal", "TypeofKPIID", "KPIID"
            ].forEach(id => {
                const el = document.getElementById(id);
                if (el) el.value = "";
            });


            document.querySelectorAll('.division-checkbox, .department-checkbox, .section-checkbox').forEach(cb => cb.checked = false);
            divisionInput.value = departmentInput.value = sectionInput.value = '';
            divisionHidden.value = departmentHidden.value = sectionHidden.value = '';
            deptList.innerHTML = '';
            secList.innerHTML = '';

            if (deleteButton) deleteButton.style.display = "none";
        });
    }

    document.addEventListener('change', function (e) {
        if (e.target.classList.contains('division-checkbox')) {
            updateSelection('division');
            loadDepartments(true);
        }
        if (e.target.classList.contains('department-checkbox')) {
            updateSelection('department');
            loadSections(false); 
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

        hidden.value = selected.join(';');
        input.value = selected.length ? `${selected.length} selected` : '';
    }


    function loadDepartments(clear = true, callback) {
        const selectedDivisions = divisionHidden.value.split(';').filter(x => x);

        if (clear) deptList.innerHTML = ''; 
        if (clear) {
            secList.innerHTML = '';
            departmentInput.value = '';
            sectionInput.value = '';
        }

        if (selectedDivisions.length === 0) {
            if (callback) callback();
            return;
        }

        let existingDepts = Array.from(document.querySelectorAll('.department-checkbox')).map(cb => cb.value);
        let requests = selectedDivisions.length;

        selectedDivisions.forEach(division => {
            $.getJSON('/TPR/GetDepartments', { division: division }, function (data) {
                data.forEach(dept => {
                    if (!existingDepts.includes(dept.ema_dept_desc)) {
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
                    }
                });
            }).always(() => {
                requests--;
                if (requests === 0 && callback) callback();
            });
        });
    }

    function loadSections(clear = true, callback) {
        const selectedDepts = Array.from(document.querySelectorAll('.department-checkbox:checked'));
        if (clear) secList.innerHTML = '';
        sectionInput.value = '';

        if (selectedDepts.length === 0) {
            if (callback) callback();
            return;
        }

        let existingSecs = Array.from(document.querySelectorAll('.section-checkbox')).map(cb => cb.value);
        let requests = selectedDepts.length;

        selectedDepts.forEach(cb => {
            const division = cb.getAttribute('data-division');
            const dept = cb.value;

            $.getJSON('/TPR/GetSections', { division: division, department: dept }, function (data) {
                data.forEach(sec => {
                    if (!existingSecs.includes(sec.ema_section_desc)) {
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
                    }
                });
            }).always(() => {
                requests--;
                if (requests === 0 && callback) callback();
            });
        });
    }

    refNoLinks.forEach(link => {
        link.addEventListener("click", function (event) {
            event.preventDefault();
            KPIMaster.style.display = "block";


            document.getElementById("KPICode").value = this.getAttribute("data-KPICode");
            document.getElementById("KPILevel").value = this.getAttribute("data-KPILevel");
            document.getElementById("Company").value = this.getAttribute("data-Company");
            document.getElementById("PerspectiveID").value = this.getAttribute("data-PerspectiveID");
            document.getElementById("TypeofKPIID").value = this.getAttribute("data-TypeofKPIID");
            document.getElementById("UnitID").value = this.getAttribute("data-UnitID");
            document.getElementById("KPIDefination").value = this.getAttribute("data-KPIDefination");
            document.getElementById("KPIDetails").value = this.getAttribute("data-KPIDetails");
            document.getElementById("PeriodicityID").value = this.getAttribute("data-PeriodicityName");
            document.getElementById("GoodPerformance").value = this.getAttribute("data-GoodPerformance");
            document.getElementById("NoofDecimal").value = this.getAttribute("data-NoofDecimal");
            document.getElementById("KPIID").value = this.getAttribute("data-id");

            const divisionValues = this.getAttribute("data-Division")?.split(';').map(v => v.trim()) || [];
            const departmentValues = this.getAttribute("data-Department")?.split(';').map(v => v.trim()) || [];
            const sectionValues = this.getAttribute("data-Section")?.split(';').map(v => v.trim()) || [];

       
            document.querySelectorAll('.division-checkbox').forEach(cb => {
                cb.checked = divisionValues.includes(cb.value.trim());
            });
            divisionHidden.value = divisionValues.join(';');
            divisionInput.value = divisionValues.length ? `${divisionValues.length} selected` : '';


            loadDepartments(false, () => {
                document.querySelectorAll('.department-checkbox').forEach(cb => {
                    cb.checked = departmentValues.includes(cb.value.trim());
                });
                departmentHidden.value = departmentValues.join(';');
                departmentInput.value = departmentValues.length ? `${departmentValues.length} selected` : '';

                loadSections(false, () => {
                    document.querySelectorAll('.section-checkbox').forEach(cb => {
                        cb.checked = sectionValues.includes(cb.value.trim());
                    });
                    sectionHidden.value = sectionValues.join(';');
                    sectionInput.value = sectionValues.length ? `${sectionValues.length} selected` : '';
                });
            });

            if (deleteButton) deleteButton.style.display = "inline-block";
        });
    });

    if (submitButton) {
        submitButton.addEventListener("click", function () {
            actionTypeInput.value = "save";
        });
    }

    if (deleteButton) {
        deleteButton.addEventListener("click", function () {
            Swal.fire({
                title: 'Are you sure?',
                text: "Do you really want to delete this Unit?",
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
