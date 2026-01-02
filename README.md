using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;
using System.Security.Claims;

[HttpPost]
public async Task<IActionResult> VerifyOtp([FromBody] OtpVerifyModel model)
{
    var sessionOtp = HttpContext.Session.GetString("LoginOTP");
    var sessionUser = HttpContext.Session.GetString("OTPUser");
    var expiryString = HttpContext.Session.GetString("OTPExpiry");

    if (sessionOtp == null || expiryString == null)
        return Json(new { success = false, message = "OTP expired. Please login again." });

    DateTime expiryTime = DateTime.Parse(
        expiryString, null,
        System.Globalization.DateTimeStyles.RoundtripKind);

    if (DateTime.UtcNow > expiryTime)
    {
        HttpContext.Session.Clear();
        return Json(new { success = false, message = "OTP expired. Please login again." });
    }

    if (sessionUser != model.UserId || sessionOtp != model.Otp)
        return Json(new { success = false, message = "Invalid OTP" });

    // Clear OTP session
    HttpContext.Session.Remove("LoginOTP");
    HttpContext.Session.Remove("OTPExpiry");

    // 🔐 CREATE AUTH COOKIE (THIS WAS MISSING)
    var claims = new List<Claim>
    {
        new Claim(ClaimTypes.Name, model.UserId),
        new Claim(ClaimTypes.NameIdentifier, model.UserId)
    };

    var identity = new ClaimsIdentity(
        claims,
        CookieAuthenticationDefaults.AuthenticationScheme);

    var principal = new ClaimsPrincipal(identity);

    await HttpContext.SignInAsync(
        CookieAuthenticationDefaults.AuthenticationScheme,
        principal,
        new AuthenticationProperties
        {
            IsPersistent = true,
            ExpiresUtc = DateTime.UtcNow.AddHours(8)
        });

    return Json(new
    {
        success = true,
        redirectUrl = "/Face/GeoFencing"
    });
}

builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.LoginPath = "/User/Login";
        options.LogoutPath = "/User/Logout";
        options.AccessDeniedPath = "/User/Login";
        options.ExpireTimeSpan = TimeSpan.FromHours(8);
        options.SlidingExpiration = true;
    });

builder.Services.AddAuthorization();

app.UseAuthentication();
app.UseAuthorization();

window.location.href = res.redirectUrl;




https://localhost:7153/User/Login?ReturnUrl=%2FFace%2FGeoFencing



 [HttpPost]
 public async Task<IActionResult> Login([FromBody] AppLogin login)
 {
     string serverCaptcha = HttpContext.Session.GetString("CaptchaCode");

     if (serverCaptcha == null || login.Captcha?.ToLower() != serverCaptcha.ToLower())
         return Json(new { success = false, message = "Invalid Captcha" });

     var user = await context.AppLogins
         .FirstOrDefaultAsync(x => x.UserId == login.UserId);

     if (user == null)
         return Json(new { success = false, message = "User not found" });


     if (user.IsLockedOut == true)
     {

         if (user.LastLockoutDate.HasValue &&
             DateTime.UtcNow > user.LastLockoutDate.Value.AddMinutes(LOCKOUT_MINUTES))
         {

             user.IsLockedOut = false;
             user.FailedPasswordAttemptCount = 0;
             await context.SaveChangesAsync();
         }
         else
         {
             return Json(new
             {
                 success = false,
                 message = "Your account is locked due to multiple failed login attempts. Please try later."
             });
         }
     }

     bool isPasswordValid = hash_Password.VerifyPassword(
         login.Password,
         user.Password,
         user.PasswordSalt
     );


     if (!isPasswordValid)
     {
         user.FailedPasswordAttemptCount ??= 0;
         user.FailedPasswordAttemptCount++;

         user.FailedPasswordAttemptWindowStart = DateTime.UtcNow;

         if (user.FailedPasswordAttemptCount >= MAX_FAILED_ATTEMPTS)
         {
             user.IsLockedOut = true;
             user.LastLockoutDate = DateTime.UtcNow;
         }

         await context.SaveChangesAsync();

         return Json(new
         {
             success = false,
             message = user.IsLockedOut == true
                 ? "Your account has been locked after 5 failed attempts."
                 : $"Incorrect password. Attempts left: {MAX_FAILED_ATTEMPTS - user.FailedPasswordAttemptCount}"
         });
     }


     user.FailedPasswordAttemptCount = 0;
     user.FailedPasswordAttemptWindowStart = null;
     user.IsLockedOut = false;
     user.LastLoginDate = DateTime.UtcNow;

     await context.SaveChangesAsync();


     string otp = new Random().Next(100000, 999999).ToString();

     HttpContext.Session.SetString("LoginOTP", otp);
     HttpContext.Session.SetString("OTPUser", login.UserId);
     HttpContext.Session.SetString("OTPExpiry",
         DateTime.UtcNow.AddMinutes(5).ToString("O"));

     string smsError;
     bool smsSent = SendSmsToUser(login.UserId, otp, out smsError);

     if (!smsSent)
         return Json(new { success = false, message = smsError });

     return Json(new { success = true, otpRequired = true });
 }



 [HttpPost]
 public IActionResult VerifyOtp([FromBody] OtpVerifyModel model)
 {
     var sessionOtp = HttpContext.Session.GetString("LoginOTP");
     var sessionUser = HttpContext.Session.GetString("OTPUser");
     var expiryString = HttpContext.Session.GetString("OTPExpiry");

     if (sessionOtp == null || expiryString == null)
         return Json(new { success = false, message = "OTP expired. Please login again." });


     DateTime expiryTime = DateTime.Parse(expiryString, null,
         System.Globalization.DateTimeStyles.RoundtripKind);

     if (DateTime.UtcNow > expiryTime)
     {
   
         HttpContext.Session.Remove("LoginOTP");
         HttpContext.Session.Remove("OTPExpiry");

         return Json(new { success = false, message = "OTP expired. Please login again." });
     }

     if (sessionUser != model.UserId || sessionOtp != model.Otp)
         return Json(new { success = false, message = "Invalid OTP" });


     HttpContext.Session.Remove("LoginOTP");
     HttpContext.Session.Remove("OTPExpiry");


     var cookieOptions = new CookieOptions
     {
         Expires = DateTimeOffset.Now.AddYears(1),
         HttpOnly = true,
         Secure = true,
         SameSite = SameSiteMode.Strict
     };

     Response.Cookies.Append("UserSession", model.UserId, cookieOptions);

     return Json(new { success = true,redirectUrl="/Face/GeoFencing" });
 }

Js

<script>
    let isLoginProcessing = false;

    function showLoading(show) {
        if (show)
            $("#loading-overlay").css("display", "flex");
        else
            $("#loading-overlay").hide();
    }


    $(document).on("keydown", function (e) {
        if (e.key === "Enter") {
            e.preventDefault();

            if ($("#otpModal").hasClass("show")) {
                $("#verifyOtpBtn").click();
            }
            else {
                $("#btnLogin").click();
            }
        }
    });


    function styleCaptcha(text) {
        const colors = ["#e74c3c", "#2980b9", "#27ae60", "#8e44ad", "#d35400", "#2c3e50"];
        let html = "";

        for (let i = 0; i < text.length; i++) {
            let color = colors[Math.floor(Math.random() * colors.length)];
            let rotate = Math.floor(Math.random() * 20 - 10);

            html += `<span style="color:${color}; display:inline-block; transform:rotate(${rotate}deg);">
                        ${text[i]}
                     </span>`;
        }
        $("#captchaDisplay").html(html);
    }

    document.addEventListener("DOMContentLoaded", function () {
        styleCaptcha($("#serverCaptcha").val());
    });

    function refreshCaptcha() {
        fetch(window.appRoot + 'User/RefreshCaptcha')
            .then(res => res.json())
            .then(data => {
                $("#serverCaptcha").val(data.captcha);
                styleCaptcha(data.captcha);
            });
    }

    $("#btnLogin").click(function (e) {
        e.preventDefault();

        if (isLoginProcessing) return;
        isLoginProcessing = true;

        let UserId = $("#UserId").val().trim();
        let password = $("#password").val().trim();
        let captchaEntered = $("#captchaInput").val().trim();
        let serverCaptcha = $("#serverCaptcha").val();

        if (!UserId || !password) {
            Swal.fire("Please enter UserId and Password");
            isLoginProcessing = false;
            return;
        }

        if (captchaEntered.toLowerCase() !== serverCaptcha.toLowerCase()) {
            Swal.fire("Invalid Captcha ❌");
            refreshCaptcha();
            $("#captchaInput").val("");
            isLoginProcessing = false;
            return;
        }

        $("#btnLogin").prop("disabled", true);
        showLoading(true);

        $.ajax({
            url: '@Url.Action("Login", "User")',
            type: 'POST',
            contentType: 'application/json',
            data: JSON.stringify({
                UserId: UserId,
                Password: password,
                Captcha: captchaEntered
            }),
            success: function (response) {
                showLoading(false);
                isLoginProcessing = false;
                $("#btnLogin").prop("disabled", false);

                if (response.success && response.otpRequired) {

                    const otpModal = new bootstrap.Modal(
                        document.getElementById('otpModal'),
                        {
                            backdrop: 'static',                         }
                    );

                    $("#otpInput").val("");
                    otpModal.show();
                    return;
                }

                Swal.fire({
                    title: response.message || "Incorrect Password",
                   html: `<img src="${window.appRoot}AppImages/img14.gif" width="150">`,
                        showConfirmButton: false,
                        timer: 4000,
                        background: '#f4f6f9',
                        backdrop: `
                            rgb(121 0 0 / 40%)                       
                            left top
                            no-repeat
                        `
                    });


                refreshCaptcha();
                $("#password").val("");
                 $("#captchaInput").val("");
            },
            error: function () {
                showLoading(false);
                isLoginProcessing = false;
                $("#btnLogin").prop("disabled", false);

                Swal.fire("Server Error ⚠️");
            }
        });
    });

    $("#verifyOtpBtn").click(function () {

        let otp = $("#otpInput").val().trim();
        let userId = $("#UserId").val().trim();

        if (!otp) {
            Swal.fire("Please enter OTP");
            return;
        }

        showLoading(true);

        $.ajax({
            url: window.appRoot + "User/VerifyOtp",
            type: "POST",
            contentType: "application/json",
            data: JSON.stringify({
                UserId: userId,
                Otp: otp
            }),
            success: function (res) {
                showLoading(false);

                if (res.success) {
                    Swal.fire({
                        title: 'Welcome Back!',
                       html: `<img src="${window.appRoot}AppImages/img9.jpg" width="150">`,
                    showConfirmButton: false,
                    timer: 2500,
                    background: '#f4f6f9',
                    backdrop: `
                            rgba(0,0,123,0.4)
                            url("/images/party.gif")
                            left top
                            no-repeat
                        `
                });


                    setTimeout(() => {
                        window.location.href = window.appRoot + "Face/GeoFencing";
                    }, 1800);
                } else {
                    Swal.fire(res.message || "Invalid or Expired OTP");
                    $("#otpInput").val("");
                }
            },
            error: function () {
                showLoading(false);
                Swal.fire("OTP verification failed");
            }
        });
    });


    function hideOtpModal() {
        const modalEl = document.getElementById('otpModal');
        const modal = bootstrap.Modal.getInstance(modalEl);
        if (modal) modal.hide();
    }
</script>
