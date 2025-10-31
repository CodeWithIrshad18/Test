<div class="row g-3 mt-1 align-items-center">
    <div class="col-md-2">
        <label class="control-label">Mode</label>
    </div>

    <!-- Mode Buttons -->
    <div class="col-md-2">
        <div class="btn-group" role="group" aria-label="Mode toggle">
            <input type="radio" class="btn-check" name="Deactivate" id="Active" value="false" autocomplete="off"
                   @(Model.Deactivate == false || Model.Deactivate == null ? "checked" : "")>
            <label class="btn btn-outline-success" for="Active">Active</label>

            <input type="radio" class="btn-check" name="Deactivate" id="Inactive" value="true" autocomplete="off"
                   @(Model.Deactivate == true ? "checked" : "")>
            <label class="btn btn-outline-danger" for="Inactive">Inactive</label>
        </div>
    </div>

    <!-- Deactivate From -->
    <div class="col-md-2 deactive-field" style="display: none;">
        <label for="DeactiveFrom" class="control-label">Deactive From</label>
        <input type="datetime-local" class="form-control form-control-sm" id="DeactiveFrom"
               name="DeactivateFrom"
               value="@(Model.DeactivateFrom?.ToString("yyyy-MM-ddTHH:mm") ?? "")">
    </div>

    <!-- Deactivate To -->
    <div class="col-md-2 deactive-field" style="display: none;">
        <label for="DeactiveTo" class="control-label">Deactive To</label>
        <input type="datetime-local" class="form-control form-control-sm" id="DeactiveTo"
               name="DeactivateTo"
               value="@(Model.DeactivateTo?.ToString("yyyy-MM-ddTHH:mm") ?? "")">
    </div>
</div>

<script>
    document.addEventListener("DOMContentLoaded", function () {
        const activeRadio = document.getElementById("Active");
        const inactiveRadio = document.getElementById("Inactive");
        const deactiveFields = document.querySelectorAll(".deactive-field");

        function toggleFields() {
            if (inactiveRadio.checked) {
                deactiveFields.forEach(field => field.style.display = "block");
            } else {
                deactiveFields.forEach(field => field.style.display = "none");
                // clear date values when switching to Active
                document.getElementById("DeactiveFrom").value = "";
                document.getElementById("DeactiveTo").value = "";
            }
        }

        activeRadio.addEventListener("change", toggleFields);
        inactiveRadio.addEventListener("change", toggleFields);

        // Initialize visibility (useful for edit mode)
        toggleFields();
    });
</script>






  <div class="row g-3 mt-1 align-items-center">
    <div class="col-md-2">
        <label for="Mode" class="control-label">Mode</label>
    </div>

    <div class="col-md-1">
        <div class="form-check">
            <input type="radio" class="btn-check" name="Deactivate" id="Active" value="false" autocomplete="off" checked>
            <label class="form-check-label" for="Active">Active</label>
        </div>
    </div>

    <div class="col-md-1">
        <div class="form-check">
            <input type="radio" class="btn-check" name="Deactivate" id="Inactive" value="true" autocomplete="off">
            <label class="form-check-label" for="Inactive">Inactive</label>
        </div>
    </div>

    <div class="col-md-2 deactive-field" style="display: none;">
        <label for="DeactiveFrom" class="control-label">Deactive From</label>
    </div>

        <div class="col-md-2 deactive-field" style="display: none;">
            <input type="datetime-local" class="form-control form-control-sm" id="DeactiveFrom" name="DeactivateFrom">
</div>

    <div class="col-md-2 deactive-field" style="display: none;">
        <label for="DeactiveTo" class="control-label">Deactive To</label>
    </div>

    <div class="col-md-2 deactive-field" style="display: none;">
         <input type="datetime-local" class="form-control form-control-sm" id="DeactiveTo" name="DeactivateTo">
         </div>
</div>

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
