<div class="row g-3 mt-1 align-items-center">
    <div class="col-md-2">
        <label for="Mode" class="control-label">Mode</label>
    </div>

    <div class="col-md-4">
        <div class="btn-group" role="group" aria-label="Mode toggle">
            <input type="checkbox" class="btn-check" id="Active" autocomplete="off">
            <label class="btn btn-outline-success" for="Active">Active</label>

            <input type="checkbox" class="btn-check" id="Inactive" autocomplete="off">
            <label class="btn btn-outline-danger" for="Inactive">Inactive</label>
        </div>
    </div>

    <!-- Hidden date fields (same row) -->
    <div class="col-md-2 deactive-field" style="display: none;">
        <label for="DeactiveFrom" class="control-label">Deactive From</label>
        <input type="datetime-local" class="form-control form-control-sm" id="DeactiveFrom" name="DeactiveFrom">
    </div>

    <div class="col-md-2 deactive-field" style="display: none;">
        <label for="DeactiveTo" class="control-label">Deactive To</label>
        <input type="datetime-local" class="form-control form-control-sm" id="DeactiveTo" name="DeactiveTo">
    </div>
</div>

<script>
    document.addEventListener("DOMContentLoaded", function () {
        const activeBtn = document.getElementById("Active");
        const inactiveBtn = document.getElementById("Inactive");
        const deactiveFields = document.querySelectorAll(".deactive-field");

        function toggleFields() {
            if (inactiveBtn.checked) {
                deactiveFields.forEach(field => field.style.display = "block");
            } else {
                deactiveFields.forEach(field => field.style.display = "none");
            }
        }

        activeBtn.addEventListener("change", function () {
            if (this.checked) {
                inactiveBtn.checked = false;
            }
            toggleFields();
        });

        inactiveBtn.addEventListener("change", function () {
            if (this.checked) {
                activeBtn.checked = false;
            }
            toggleFields();
        });
    });
</script>

<div class="row g-3 mt-1 align-items-center">
    <div class="col-md-2">
        <label for="Mode" class="control-label">Mode</label>
    </div>

    <div class="col-md-2">
        <div class="form-check">
            <input class="form-check-input" type="checkbox" id="Active" name="Mode" value="Active">
            <label class="form-check-label" for="Active">Active</label>
        </div>
    </div>

    <div class="col-md-2">
        <div class="form-check">
            <input class="form-check-input" type="checkbox" id="Inactive" name="Mode" value="Inactive">
            <label class="form-check-label" for="Inactive">Inactive</label>
        </div>
    </div>

    <!-- Hidden date fields (same row) -->
    <div class="col-md-2 deactive-field" style="display: none;">
        <label for="DeactiveFrom" class="control-label">Deactive From</label>
        <input type="datetime-local" class="form-control form-control-sm" id="DeactiveFrom" name="DeactiveFrom">
    </div>

    <div class="col-md-2 deactive-field" style="display: none;">
        <label for="DeactiveTo" class="control-label">Deactive To</label>
        <input type="datetime-local" class="form-control form-control-sm" id="DeactiveTo" name="DeactiveTo">
    </div>
</div>

<script>
    document.addEventListener("DOMContentLoaded", function () {
        const activeCheckbox = document.getElementById("Active");
        const inactiveCheckbox = document.getElementById("Inactive");
        const deactiveFields = document.querySelectorAll(".deactive-field");

        function toggleFields() {
            if (inactiveCheckbox.checked) {
                deactiveFields.forEach(field => field.style.display = "block");
            } else {
                deactiveFields.forEach(field => field.style.display = "none");
            }
        }

        activeCheckbox.addEventListener("change", function () {
            if (this.checked) {
                inactiveCheckbox.checked = false;
            }
            toggleFields();
        });

        inactiveCheckbox.addEventListener("change", function () {
            if (this.checked) {
                activeCheckbox.checked = false;
            }
            toggleFields();
        });
    });
</script>





<div class="row g-3 mt-1">
    <div class="col-md-2">
        <label for="Mode" class="control-label">Mode</label>
    </div>

    <div class="col-md-4">
        <div class="form-check form-check-inline">
            <input class="form-check-input" type="checkbox" id="Active" name="Mode" value="Active">
            <label class="form-check-label" for="Active">Active</label>
        </div>

        <div class="form-check form-check-inline">
            <input class="form-check-input" type="checkbox" id="Inactive" name="Mode" value="Inactive">
            <label class="form-check-label" for="Inactive">Inactive</label>
        </div>
    </div>
</div>

<!-- Hidden date fields -->
<div class="row g-3 mt-1" id="deactiveFields" style="display: none;">
    <div class="col-md-2">
        <label for="DeactiveFrom" class="control-label">Deactive From</label>
    </div>
    <div class="col-md-3">
        <input type="datetime-local" class="form-control form-control-sm" id="DeactiveFrom" name="DeactiveFrom">
    </div>

    <div class="col-md-2">
        <label for="DeactiveTo" class="control-label">Deactive To</label>
    </div>
    <div class="col-md-3">
        <input type="datetime-local" class="form-control form-control-sm" id="DeactiveTo" name="DeactiveTo">
    </div>
</div>

<script>
    $(document).ready(function () {
        // Allow only one checkbox at a time
        $("input[name='Mode']").on('change', function () {
            $("input[name='Mode']").not(this).prop('checked', false);

            if ($("#Inactive").is(":checked")) {
                $("#deactiveFields").slideDown();
            } else {
                $("#deactiveFields").slideUp();
            }
        });
    });
</script>

        
        
        
        
        <div class="row g-3 mt-1">
            <div class="col-md-2">
<label for="Mode" class="control-label">Mode</label>
</div>
 <div class="col-md-2">
 
   </div>
            </div>

i want two checkbox one for Active and another InActive , if checkbox Active is select then show two input fields with DeactiveFrom and DeactiveTo datetime property 
