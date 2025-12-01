    <form asp-action="ChangePassword" asp-controller="User" method="post">
        <div class="d-flex justify-content-center" style="margin-top:60px;">
            @if (ViewBag.FailedMsg != null)
            {
            <div class="alert alert-danger" style="font-family:Arial;font-size:13px;">
                @ViewBag.FailedMsg
            </div>
            }
            @if (ViewBag.ChangePass != null)
            {
            <div class="alert alert-success">
                @ViewBag.ChangePass
            </div>
            }
        </div>

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
<script>

    document.addEventListener("DOMContentLoaded", function () {
        var newPasswordInput = document.getElementById("newPassword");
        var confirmPasswordInput = document.getElementById("confirmPassword");
        var errorMessage = document.getElementById("passwordMatchError");
        var form = document.querySelector("form");

        function validatePasswords() {
            if (newPasswordInput.value !== confirmPasswordInput.value) {
                errorMessage.style.display = "block"; 
                return false;
            } else {
                errorMessage.style.display = "none"; 
                return true;
            }
        }

        
        confirmPasswordInput.addEventListener("input", validatePasswords);

     
        form.addEventListener("submit", function (event) {
            if (!validatePasswords()) {
                event.preventDefault(); 
            }
        });
    });



</script>
