using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public async Task<IActionResult> Logout()
{
    // Sign out from authentication middleware
    await HttpContext.SignOutAsync(CookieAuthenticationDefaults.AuthenticationScheme);

    // Clear session
    HttpContext.Session.Clear();

    // Delete all cookies explicitly (extra safety)
    foreach (var cookie in Request.Cookies.Keys)
    {
        Response.Cookies.Delete(cookie);
    }

    return RedirectToAction("Login", "User");
}

app.Use(async (context, next) =>
{
    context.Response.Headers["Cache-Control"] = "no-cache, no-store";
    context.Response.Headers["Pragma"] = "no-cache";
    context.Response.Headers["Expires"] = "0";
    await next();
});



this is my login which has cookies and all 
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

and this is my logout 
 public IActionResult Logout()
 {

     return RedirectToAction("Login", "User");

 }


i want my logout works real like remove cookies and all 
