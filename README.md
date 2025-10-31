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
