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

    // helper to set counts and hidden fields
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

    // load departments for currently selected divisions
    // clear === true -> wipe deptList, secList and hidden fields
    // clear === false -> append and keep hidden fields (used by edit / refNo load)
    function loadDepartments(clear = true, callback) {
        const selectedDivisions = divisionHidden.value.split(';').filter(x => x);

        if (clear) {
            deptList.innerHTML = '';
            secList.innerHTML = '';
            departmentInput.value = '';
            sectionInput.value = '';
            departmentHidden.value = '';
            sectionHidden.value = '';
        }

        if (selectedDivisions.length === 0) {
            // ensure department/section counts reflect reality
            updateSelection('department');
            updateSelection('section');
            if (callback) callback();
            return;
        }

        // gather current existing dept values (to avoid duplicates)
        let existingDepts = Array.from(document.querySelectorAll('.department-checkbox')).map(cb => cb.value);
        let requests = selectedDivisions.length;

        selectedDivisions.forEach(division => {
            $.getJSON('/TPR/GetDepartments', { division: division }, function (data) {
                data.forEach(dept => {
                    if (!existingDepts.includes(dept.ema_dept_desc)) {
                        deptList.insertAdjacentHTML('beforeend', `
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
                            </li>`);
                    }
                });
            }).always(() => {
                requests--;
                if (requests === 0) {
                    // if we called with clear=true, we removed all and re-built, but some departments
                    // could belong to now-unselected divisions if clear===false and previously items existed.
                    // Remove any department whose data-division is not in selectedDivisions.
                    document.querySelectorAll('.department-checkbox').forEach(cb => {
                        const cbDiv = cb.getAttribute('data-division');
                        if (!selectedDivisions.includes(cbDiv)) {
                            const li = cb.closest('li');
                            if (li) li.remove();
                        }
                    });

                    // update hidden value & count immediately to avoid ghost '1 selected'
                    updateSelection('department');

                    // After departments updated, load sections for currently checked departments.
                    // For edit flow we usually want loadSections(false) so existing section hidden values aren't wiped.
                    if (callback) callback();
                }
            });
        });
    }

    // load sections for currently checked departments
    // clear === true -> wipe secList and section hidden
    // clear === false -> append and keep hidden fields
    function loadSections(clear = true, callback) {
        const checkedDeptCheckboxes = Array.from(document.querySelectorAll('.department-checkbox:checked'));

        if (clear) {
            secList.innerHTML = '';
            sectionInput.value = '';
            sectionHidden.value = '';
        }

        if (checkedDeptCheckboxes.length === 0) {
            updateSelection('section');
            if (callback) callback();
            return;
        }

        let existingSecs = Array.from(document.querySelectorAll('.section-checkbox')).map(cb => cb.value);
        let requests = checkedDeptCheckboxes.length;

        checkedDeptCheckboxes.forEach(cb => {
            const division = cb.getAttribute('data-division');
            const dept = cb.value;

            $.getJSON('/TPR/GetSections', { division: division, department: dept }, function (data) {
                data.forEach(sec => {
                    if (!existingSecs.includes(sec.ema_section_desc)) {
                        // include data-department so removal logic can work if dept unchecked
                        secList.insertAdjacentHTML('beforeend', `
                            <li style="margin-left:5%;">
                                <div class="form-check">
                                    <input type="checkbox" class="form-check-input section-checkbox"
                                        data-department="${dept}"
                                        value="${sec.ema_section_desc}"
                                        id="sec_${sec.ema_section_desc.replace(/\s+/g, '_')}">
                                    <label class="form-check-label" for="sec_${sec.ema_section_desc.replace(/\s+/g, '_')}">
                                        ${sec.ema_section_desc}
                                    </label>
                                </div>
                            </li>`);
                    }
                });
            }).always(() => {
                requests--;
                if (requests === 0) {
                    // Remove sections whose department is no longer checked
                    const validDeptValues = Array.from(document.querySelectorAll('.department-checkbox:checked')).map(d => d.value);
                    document.querySelectorAll('.section-checkbox').forEach(cb => {
                        const secDept = cb.getAttribute('data-department');
                        if (secDept && !validDeptValues.includes(secDept)) {
                            const li = cb.closest('li');
                            if (li) li.remove();
                        }
                    });

                    updateSelection('section');
                    if (callback) callback();
                }
            });
        });
    }

    // When user checks/unchecks checkboxes in UI
    document.addEventListener('change', function (e) {
        if (e.target.classList.contains('division-checkbox')) {
            // keep division hidden + input up to date
            updateSelection('division');
            // rebuild departments based on new divisions (clear previous dept/section)
            loadDepartments(true, () => {
                // wire up any department -> if some departments remain checked (possible if same division),
                // ensure sections are refreshed
                updateSelection('department');
                loadSections(true);
            });
        }

        if (e.target.classList.contains('department-checkbox')) {
            updateSelection('department');
            // rebuild sections based on checked departments (clear previous sections)
            loadSections(true);
        }

        if (e.target.classList.contains('section-checkbox')) {
            updateSelection('section');
        }
    });

    // NEW button: fully clear everything (user wanted this)
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

            // Uncheck every checkbox in DOM and clear UI lists & hidden fields
            document.querySelectorAll('.division-checkbox, .department-checkbox, .section-checkbox').forEach(cb => cb.checked = false);
            divisionInput.value = departmentInput.value = sectionInput.value = '';
            divisionHidden.value = departmentHidden.value = sectionHidden.value = '';
            deptList.innerHTML = '';
            secList.innerHTML = '';

            if (deleteButton) deleteButton.style.display = "none";
        });
    }

    // When clicking an existing record (refNoLink) -> populate values and keep hidden fields intact.
    refNoLinks.forEach(link => {
        link.addEventListener("click", function (event) {
            event.preventDefault();
            KPIMaster.style.display = "block";

            // populate basic fields
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

            // Parse saved values
            const divisionValues = this.getAttribute("data-Division")?.split(';').map(v => v.trim()).filter(v=>v) || [];
            const departmentValues = this.getAttribute("data-Department")?.split(';').map(v => v.trim()).filter(v=>v) || [];
            const sectionValues = this.getAttribute("data-Section")?.split(';').map(v => v.trim()).filter(v=>v) || [];

            // Set division checkboxes in DOM according to saved divisions (do not clear hidden here)
            document.querySelectorAll('.division-checkbox').forEach(cb => {
                cb.checked = divisionValues.includes(cb.value.trim());
            });

            // Update hidden and input for divisions
            divisionHidden.value = divisionValues.join(';');
            divisionInput.value = divisionValues.length ? `${divisionValues.length} selected` : '';

            // LOAD departments for the selected divisions WITHOUT clearing hidden fields (clear=false)
            // After load, check the department boxes that were saved
            loadDepartments(false, () => {
                document.querySelectorAll('.department-checkbox').forEach(cb => {
                    cb.checked = departmentValues.includes(cb.value.trim());
                });
                departmentHidden.value = departmentValues.join(';');
                departmentInput.value = departmentValues.length ? `${departmentValues.length} selected` : '';

                // LOAD sections for the selected departments WITHOUT clearing hidden fields
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





if (e.target.classList.contains('division-checkbox')) {
    updateSelection('division');
    loadDepartments(true); // clear previous departments properly
}


function loadDepartments(clear = true, callback) {
    const selectedDivisions = divisionHidden.value.split(';').filter(x => x);

    // Clear departments and sections if needed
    if (clear) {
        deptList.innerHTML = '';
        secList.innerHTML = '';
        departmentInput.value = '';
        sectionInput.value = '';
        departmentHidden.value = '';
        sectionHidden.value = '';
    }

    if (selectedDivisions.length === 0) {
        updateSelection('department');
        updateSelection('section');
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
            if (requests === 0) {
                // Remove departments from unchecked divisions
                document.querySelectorAll('.department-checkbox').forEach(cb => {
                    const cbDiv = cb.getAttribute('data-division');
                    if (!selectedDivisions.includes(cbDiv)) {
                        cb.closest('li').remove();
                    }
                });

                updateSelection('department'); // update hidden + input count
                if (callback) callback();
            }
        });
    });
}

function loadSections(clear = true, callback) {
    const selectedDepts = Array.from(document.querySelectorAll('.department-checkbox:checked'));
    if (clear) {
        secList.innerHTML = '';
        sectionInput.value = '';
        sectionHidden.value = '';
    }

    if (selectedDepts.length === 0) {
        updateSelection('section');
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
            if (requests === 0) {
                // Remove sections that belong to unselected departments
                const validDeptValues = selectedDepts.map(cb => cb.value);
                document.querySelectorAll('.section-checkbox').forEach(cb => {
                    const secDept = cb.getAttribute('data-department');
                    if (secDept && !validDeptValues.includes(secDept)) {
                        cb.closest('li').remove();
                    }
                });

                updateSelection('section');
                if (callback) callback();
            }
        });
    });
}




this is my js

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
            loadDepartments(false);
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
