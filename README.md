i have this two js 
<script>

    document.addEventListener("DOMContentLoaded", function () {
        var newButton = document.getElementById("newButton");
        var KPIMaster = document.getElementById("form");
        var refNoLinks = document.querySelectorAll(".refNoLink");
        var deleteButton = document.getElementById("deleteButton");
        var submitButton = document.getElementById("submitButton");
        var actionTypeInput = document.getElementById("actionType");

        if (newButton) {
            newButton.addEventListener("click", function () {
                KPIMaster.style.display = "block";
                document.getElementById("KPICode").value = "";
                document.getElementById("KPILevel").value = "";
                document.getElementById("Company").value = "";            
                document.getElementById("Division").value = "";            
                document.getElementById("Department").value = "";            
                document.getElementById("Section").value = "";            
                document.getElementById("PerspectiveID").value = "";            
                document.getElementById("UnitID").value = "";            
                document.getElementById("KPIDefination").value = "";            
                document.getElementById("KPIDetails").value = "";            
                document.getElementById("PeriodicityID").value = "";            
                document.getElementById("GoodPerformance").value = "";            
                document.getElementById("NoofDecimal").value = "";                   
                document.getElementById("TypeofKPIID").value = "";
                document.getElementById("KPIID").value = "";
                deleteButton.style.display = "none";
            });
        }

        refNoLinks.forEach(link => {
    link.addEventListener("click", function (event) {
        event.preventDefault();
        KPIMaster.style.display = "block";

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

        
        const divisionValues = this.getAttribute("data-Division")?.split(';').map(v => v.trim()) || [];
        document.querySelectorAll('.division-checkbox').forEach(cb => {
            cb.checked = divisionValues.includes(cb.value.trim());
        });

 
        const divisionInput = document.getElementById('divisionDropdown');
        const divisionHidden = document.getElementById('Division');
        const selectedDivisions = Array.from(document.querySelectorAll('.division-checkbox:checked')).map(cb => cb.value);
        divisionHidden.value = selectedDivisions.join(';');
        divisionInput.value = selectedDivisions.length ? `${selectedDivisions.length} selected` : '';

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

        hidden.value = selected.join(';');
        input.value = selected.length ? `${selected.length} selected` : '';
    }

    function loadDepartments() {
        const selectedDivisions = divisionHidden.value.split(';').filter(x => x);
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

});
</script>

i just want to show dropdown selected checkbox according to value and change behaviour according to logic in these js 
