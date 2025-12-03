@if (ViewBag.Permission != null && ViewBag.Permission.Type == "admin")
{
    <div id="HoldSection">
        <div class="row g-3 mt-1 align-items-center mb-3">
            <div class="col-md-1">
                <label for="HOLD" class="control-label">HOLD</label>
            </div>

            <div class="col-md-1">
                <div class="form-check">
                    <input type="radio" class="form-check-input" name="Hold" id="NO" value="false" checked>
                    <label class="form-check-label" for="NO">NO</label>
                </div>
            </div>

            <div class="col-md-1">
                <div class="form-check">
                    <input type="radio" class="form-check-input" name="Hold" id="YES" value="true">
                    <label class="form-check-label" for="YES">YES</label>
                </div>
            </div>

            <div class="col-md-1"></div>

            <div class="col-md-1 deactive-field">
                <label for="HoldReason" class="control-label">Hold Reason</label>
            </div>

            <div class="col-md-3 deactive-field">
                <input asp-for="HoldReason" class="form-control form-control-sm" id="HoldReason" name="HoldReason">
            </div>
        </div>
    </div>
}

 
 
 
 <div id="HoldSection">
 <div class="row g-3 mt-1 align-items-center mb-3">
    <div class="col-md-1">
        <label for="HOLD" class="control-label">HOLD</label>
    </div>

    <div class="col-md-1">
        <div class="form-check">
            <input type="radio" class="form-check-input" name="Hold" id="NO" value="false" autocomplete="off" checked>
            <label class="form-check-label" for="NO">NO</label>
        </div>
    </div>

    <div class="col-md-1">
        <div class="form-check">
            <input type="radio" class="form-check-input" name="Hold" id="YES" value="true" autocomplete="off">
            <label class="form-check-label" for="YES">YES</label>
        </div>
    </div>

     <div class="col-md-1">
    </div>
    <div class="col-md-1 deactive-field" style="display: ;">
        <label for="HoldReason" class="control-label">Hold Reason</label>
    </div>

    <div class="col-md-3 deactive-field" style="display: ;">
        <input asp-for="HoldReason" class="form-control form-control-sm" id="HoldReason" name="HoldReason" autocomplete="off">
    </div>
</div>



 var allowedPermissions = await context.AppPermissionMasters
     .Where(x => x.Pno == UserId)
           .Select(x => new PermissionInfo
           {
               Pno = x.Pno,
               Type = x.Type
           })
           .FirstOrDefaultAsync();

 ViewBag.Permission = allowedPermissions;
