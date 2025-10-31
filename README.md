
data-deactivate="True" data-deactivatefrom="31-10-2025 11:27:00" data-deactivateto="13-11-2025 11:27:00"


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
                "GoodPerformance", "NoofDecimal", "TypeofKPIID", "KPIID","KPISPOC","ImmediateSuperior","HOD","DeactiveFrom","DeactiveTo"
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
        document.getElementById("KPISPOC").value = this.getAttribute("data-KPISPOC");
        document.getElementById("ImmediateSuperior").value = this.getAttribute("data-ImmediateSuperior");
        document.getElementById("HOD").value = this.getAttribute("data-HOD");
        document.getElementById("NoofDecimal").value = this.getAttribute("data-NoofDecimal");
        document.getElementById("KPIID").value = this.getAttribute("data-id");

        const deactivate = this.getAttribute("data-Deactivate");
        const deactivateFrom = this.getAttribute("data-DeactivateFrom");
        const deactivateTo = this.getAttribute("data-DeactivateTo");

        const activeRadio = document.getElementById("Active");
        const inactiveRadio = document.getElementById("Inactive");
        const deactiveFields = document.querySelectorAll(".deactive-field");

        if (deactivate === "true") {
            inactiveRadio.checked = true;
            activeRadio.checked = false;
            deactiveFields.forEach(f => f.style.display = "block");
            document.getElementById("DeactivateFrom").value = deactivateFrom || "";
            document.getElementById("DeactivateTo").value = deactivateTo || "";
        } else {
            activeRadio.checked = true;
            inactiveRadio.checked = false;
            deactiveFields.forEach(f => f.style.display = "none");
            document.getElementById("DeactivateTo").value = "";
            document.getElementById("DeactivateTo").value = "";
        }

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

// === Handle manual toggle of Active / Inactive ===
const activeRadio = document.getElementById("Active");
const inactiveRadio = document.getElementById("Inactive");
const deactiveFields = document.querySelectorAll(".deactive-field");

// Function to show/hide date fields dynamically
function updateDeactivateFields() {
    if (inactiveRadio.checked) {
        deactiveFields.forEach(f => f.style.display = "block");
    } else {
        deactiveFields.forEach(f => f.style.display = "none");
        document.getElementById("DeactivateFrom").value = "";
        document.getElementById("DeactivateTo").value = "";
    }
}

// Attach event listeners
activeRadio.addEventListener("change", updateDeactivateFields);
inactiveRadio.addEventListener("change", updateDeactivateFields);

// Initialize on page load (in case form preloaded)
updateDeactivateFields();

    if (submitButton) {
        submitButton.addEventListener("click", function () {
            actionTypeInput.value = "save";
        });
    }

    if (deleteButton) {
        deleteButton.addEventListener("click", function () {
            Swal.fire({
                title: 'Are you sure?',
                text: "Do you really want to delete this KPI?",
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
