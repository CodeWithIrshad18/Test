<script>
document.addEventListener("DOMContentLoaded", function () {

    var newPasswordInput = document.getElementById("newPassword");
    var confirmPasswordInput = document.getElementById("confirmPassword");
    var errorMatch = document.getElementById("passwordMatchError");

    // Create strong password error below new password field
    var strongError = document.createElement("span");
    strongError.id = "passwordStrengthError";
    strongError.style.color = "red";
    strongError.style.display = "none";
    strongError.style.fontSize = "12px";
    newPasswordInput.parentNode.appendChild(strongError);

    // Strong password regex
    var strongPasswordRegex = /^(?=.*[A-Z])(?=.*[!@#$%^&*?])[A-Za-z0-9!@#$%^&*?]{8,}$/;

    function validateStrongPassword() {
        if (!strongPasswordRegex.test(newPasswordInput.value)) {
            strongError.innerText = "Password must be at least 8 characters, include 1 capital letter and 1 special character.";
            strongError.style.display = "block";
            return false;
        } else {
            strongError.style.display = "none";
            return true;
        }
    }

    function validatePasswordsMatch() {
        if (newPasswordInput.value !== confirmPasswordInput.value) {
            errorMatch.style.display = "block";
            return false;
        } else {
            errorMatch.style.display = "none";
            return true;
        }
    }

    // Live validation
    newPasswordInput.addEventListener("input", validateStrongPassword);
    confirmPasswordInput.addEventListener("input", validatePasswordsMatch);

    // Final form check
    document.querySelector("form").addEventListener("submit", function (event) {
        if (!validateStrongPassword() || !validatePasswordsMatch()) {
            event.preventDefault();
        }
    });

});
</script>

    
    
    
    
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
