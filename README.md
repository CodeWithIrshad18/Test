function loadDepartments(callback) {
    const selectedDivisions = divisionHidden.value.split(';').filter(x => x);
    const deptList = document.getElementById('departmentList');
    const secList = document.getElementById('sectionList');
    deptList.innerHTML = '';
    secList.innerHTML = '';
    departmentInput.value = '';
    sectionInput.value = '';

    if (selectedDivisions.length === 0) {
        if (callback) callback();
        return;
    }

    let requests = selectedDivisions.length;
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
        }).always(() => {
            requests--;
            if (requests === 0 && callback) callback();
        });
    });
}

function loadSections(callback) {
    const selectedDepts = Array.from(document.querySelectorAll('.department-checkbox:checked'));
    const secList = document.getElementById('sectionList');
    secList.innerHTML = '';
    sectionInput.value = '';

    if (selectedDepts.length === 0) {
        if (callback) callback();
        return;
    }

    let requests = selectedDepts.length;
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

        // ----- Assign form fields -----
        document.getElementById("KPICode").value = this.getAttribute("data-KPICode");
        document.getElementById("KPILevel").value = this.getAttribute("data-KPILevel");
        document.getElementById("Company").value = this.getAttribute("data-Company");
        document.getElementById("Division").value = this.getAttribute("data-Division");
        document.getElementById("Department").value = this.getAttribute("data-Department");
        document.getElementById("Section").value = this.getAttribute("data-Section");
        document.getElementById("PerspectiveID").value = this.getAttribute("data-PerspectiveID");
        document.getElementById("TypeofKPIID").value = this.getAttribute("data-TypeofKPIID");
        document.getElementById("UnitID").value = this.getAttribute("data-UnitID");
        document.getElementById("KPIDefination").value = this.getAttribute("data-KPIDefination");
        document.getElementById("KPIDetails").value = this.getAttribute("data-KPIDetails");
        document.getElementById("PeriodicityID").value = this.getAttribute("data-PeriodicityName");
        document.getElementById("GoodPerformance").value = this.getAttribute("data-GoodPerformance");
        document.getElementById("NoofDecimal").value = this.getAttribute("data-NoofDecimal");
        document.getElementById("KPIID").value = this.getAttribute("data-id");

        // 🔹 Division
        const divisionValues = this.getAttribute("data-Division")?.split(';').map(v => v.trim()) || [];
        document.querySelectorAll('.division-checkbox').forEach(cb => {
            cb.checked = divisionValues.includes(cb.value.trim());
        });

        const divisionInput = document.getElementById('divisionDropdown');
        const divisionHidden = document.getElementById('Division');
        const selectedDivisions = Array.from(document.querySelectorAll('.division-checkbox:checked')).map(cb => cb.value);
        divisionHidden.value = selectedDivisions.join(';');
        divisionInput.value = selectedDivisions.length ? `${selectedDivisions.length} selected` : '';

        // ✅ Load departments dynamically after divisions selected
        loadDepartments(() => {
            // 🔹 Department
            const deptValues = this.getAttribute("data-Department")
                ?.replace(/&amp;/g, "&")
                .split(',')
                .map(v => v.trim()) || [];

            document.querySelectorAll('.department-checkbox').forEach(cb => {
                cb.checked = deptValues.includes(cb.value.trim());
            });

            const departmentInput = document.getElementById('departmentDropdown');
            const departmentHidden = document.getElementById('Department');
            const selectedDepartments = Array.from(document.querySelectorAll('.department-checkbox:checked')).map(cb => cb.value);
            departmentHidden.value = selectedDepartments.join(';');
            departmentInput.value = selectedDepartments.length ? `${selectedDepartments.length} selected` : '';

            // ✅ Load sections dynamically after departments selected
            loadSections(() => {
                // 🔹 Section
                const sectionValues = this.getAttribute("data-Section")
                    ?.replace(/&amp;/g, "&")
                    .split(',')
                    .map(v => v.trim()) || [];

                document.querySelectorAll('.section-checkbox').forEach(cb => {
                    cb.checked = sectionValues.includes(cb.value.trim());
                });

                const sectionInput = document.getElementById('sectionDropdown');
                const sectionHidden = document.getElementById('Section');
                const selectedSections = Array.from(document.querySelectorAll('.section-checkbox:checked')).map(cb => cb.value);
                sectionHidden.value = selectedSections.join(';');
                sectionInput.value = selectedSections.length ? `${selectedSections.length} selected` : '';
            });
        });

        if (deleteButton) {
            deleteButton.style.display = "inline-block";
        }
    });
});




data-department="Corporate Social Responsibility,Human Resource &amp; Industrial Relations" 

data-section="Industrial Relations,Learning &amp; Development"
