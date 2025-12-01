    <div class="wrapper login" style="margin-top:60px;">
        <div class="d-flex justify-content-center">
            <i class="bx bx-lock" style="font-size:45px;color:black"></i>
        </div>

        <div class="input-box">
            <span class="icon"><ion-icon name="lock-closed-outline"></ion-icon></span>
            <input asp-for="Password" type="password" required autocomplete="off">
            <label>Enter Old Password</label>
        </div>
        <div class="input-box">
            <span class="icon"><ion-icon name="lock-closed-outline"></ion-icon></span>
            <input asp-for="NewPassword" type="password" id="newPassword" required autocomplete="off">
            <label>Enter New Password</label>
        </div>
        <div class="input-box">
            <span class="icon"><ion-icon name="lock-closed-outline"></ion-icon></span>
            <input asp-for="ConfirmPassword" type="password" id="confirmPassword" required autocomplete="off">
            <span id="passwordMatchError" style="color: red; display: none;">Passwords do not match!</span>


            <label>Confirm New Password</label>
        </div>
        <span id="Email" style="color:red;font-size:12px;" class="">@ViewBag.Msg</span>
        
        <div class="text-center">
            <button type="submit" class="btn btn-dark col-sm-4">Submit</button>
        </div>
    </div>
</form>
