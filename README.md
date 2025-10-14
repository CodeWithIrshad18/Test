please check my js 
<script>
document.addEventListener("DOMContentLoaded", function () {
 
    const KPIMaster = document.getElementById("form");
    const deleteButton = document.getElementById("deleteButton");
    const refNoLinks = document.querySelectorAll(".refNoLink");

    const divisionInput = document.getElementById('divisionDropdown');
    const departmentInput = document.getElementById('departmentDropdown');
    const sectionInput = document.getElementById('sectionDropdown');

    const divisionHidden = document.getElementById('Division');
    const departmentHidden = document.getElementById('Department');
    const sectionHidden = document.getElementById('Section');

    const deptList = document.getElementById('departmentList');
    const secList = document.getElementById('sectionList');

  
    function loadDepartments(callback) {
        const selectedDivisions = divisionHidden.value.split(';').filter(x => x);
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
            document.querySelectorAll('.division-checkbox').forEach(cb => {
                cb.checked = divisionValues.includes(cb.value.trim());
            });
            const selectedDivisions = Array.from(document.querySelectorAll('.division-checkbox:checked')).map(cb => cb.value);
            divisionHidden.value = selectedDivisions.join(';');
            divisionInput.value = selectedDivisions.length ? `${selectedDivisions.length} selected` : '';

          
            loadDepartments(() => {
                const deptValues = this.getAttribute("data-department")?.split(',').map(v => v.trim().replace(/&amp;/g, '&')) || [];
                document.querySelectorAll('.department-checkbox').forEach(cb => {
                    cb.checked = deptValues.includes(cb.value.trim());
                });
                const selectedDepartments = Array.from(document.querySelectorAll('.department-checkbox:checked')).map(cb => cb.value);
                departmentHidden.value = selectedDepartments.join(';');
                departmentInput.value = selectedDepartments.length ? `${selectedDepartments.length} selected` : '';

             
                loadSections(() => {
                    const sectionValues = this.getAttribute("data-section")?.split(',').map(v => v.trim().replace(/&amp;/g, '&')) || [];
                    document.querySelectorAll('.section-checkbox').forEach(cb => {
                        cb.checked = sectionValues.includes(cb.value.trim());
                    });
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
});
</script>

this is my dropdown 

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


and this is data-attribute 
data-Department="@item.Department"
data-Section="@item.Section"

division is working properly it checked according to value but department and Sections are not showing 
