                              <div class="row g-3 mt-1 align-items-center">
    <div class="col-md-2">
        <label for="Mode" class="control-label">Mode</label>
    </div>

    <div class="col-md-1">
        <div class="form-check">
            <input type="radio" class="form-check-input" name="Deactivate" id="Active" value="false" autocomplete="off" checked>
            <label class="form-check-label" for="Active">Active</label>
        </div>
    </div>

    <div class="col-md-1">
        <div class="form-check">
            <input type="radio" class="form-check-input" name="Deactivate" id="Inactive" value="true" autocomplete="off">
            <label class="form-check-label" for="Inactive">Inactive</label>
        </div>
    </div>

    <div class="col-md-2 deactive-field" style="display: none;">
        <label for="DeactiveFrom" class="control-label">Deactive From</label>
    </div>

    <div class="col-md-2 deactive-field" style="display: none;">
        <input type="datetime-local" class="form-control form-control-sm" id="DeactivateFrom" name="DeactivateFrom">
    </div>

    <div class="col-md-2 deactive-field" style="display: none;">
        <label for="DeactiveTo" class="control-label">Deactive To</label>
    </div>

    <div class="col-md-2 deactive-field" style="display: none;">
        <input type="datetime-local" class="form-control form-control-sm" id="DeactivateTo" name="DeactivateTo">
    </div>
</div>
