<div class="container">

    <div class="form-container sign-in-container">
        <form asp-action="Signup" asp-controller="User" id="form">
            <h1>Sign Up</h1>
            @if (ViewBag.Data != null)
            {
                <div class="alert alert-success">
                    @ViewBag.Data
                </div>
            }
            <input asp-for="UserId" class="text-center" placeholder="Personal No." type="number"
                   oninput="javascript: if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);"
                   maxlength="6" minlength="6" autocomplete="off" id="pno" />


            @if (ViewBag.DupData != null)
            {
                <div class="span">
                    <span style="color: red;">@ViewBag.DupData</span>
                </div>
            }
            <span id="UserIdError" style="color: red; display: none;">UserId must be exactly 6 digits</span>

            <input asp-for="Email" type="email" class="text-center" placeholder="Email" autocomplete="off" />

            <input asp-for="Password" type="password" class="text-center" placeholder="Password" autocomplete="off" id="Password" />

            <input asp-for="ConfirmPassword" type="password" class="text-center" placeholder="Confirm New Password" autocomplete="off" id="confirmPassword" />
            <span id="passwordMatchError" style="color: red; display: none;">Passwords do not match!</span>
            <a href="~/User/Login">Back to Login!</a>
            <button type="submit">Submit</button>
        </form>
    </div>

    <div class="overlay-container">
        <div class="overlay">
            <div class="overlay-panel overlay-left">
            </div>
            <div class="overlay-panel overlay-right">
                <img src="~/AppImages/logo2.png" width="120%" style="background-color:#fff;" />
            </div>
        </div>
    </div>
</div>
</div>


<script>
    document.addEventListener("DOMContentLoaded", function () {
        var newPasswordInput = document.getElementById("Password");
        var confirmPasswordInput = document.getElementById("confirmPassword");
        var errorMessage = document.getElementById("passwordMatchError");
        var userIdInput = document.getElementById("pno");
        var emailInput = document.querySelector('input[type="email"]');
        var userIdError = document.getElementById("UserIdError");
        var form = document.getElementById("form");

        function validateUserId() {
            var userId = userIdInput.value;
            if (userId.length !== 6 || !/^\d{6}$/.test(userId)) {
                userIdError.style.display = "block";
                userIdInput.classList.add('is-invalid');
                return false;
            } else {
                userIdError.style.display = "none";
                userIdInput.classList.remove('is-invalid');
                return true;
            }
        }

        function validatePasswords() {
            if (newPasswordInput.value !== confirmPasswordInput.value) {
                errorMessage.style.display = "block";
                return false;
            } else {
                errorMessage.style.display = "none";
                return true;
            }
        }

        function validateRequiredFields() {
            var isValid = true;
            var requiredFields = [userIdInput, emailInput, newPasswordInput, confirmPasswordInput];

            requiredFields.forEach(function (input) {
                if (input.value.trim() === "") {
                    input.classList.add('is-invalid');
                    isValid = false;
                } else {
                    input.classList.remove('is-invalid');
                }
            });

            return isValid;
        }

        userIdInput.addEventListener("input", validateUserId);
        confirmPasswordInput.addEventListener("input", validatePasswords);

        form.addEventListener("submit", function (event) {
            var isFormValid = validateRequiredFields() && validateUserId() && validatePasswords();

            if (!isFormValid) {
                event.preventDefault(); // Prevent form submission if validation fails
            }
        });
    });

</script>
