private const int MAX_FAILED_ATTEMPTS = 5;
private const int LOCKOUT_MINUTES = 15; // change to 0 if you want manual unlock only


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

    // 🔒 CHECK IF USER IS LOCKED
    if (user.IsLockedOut == true)
    {
        // Optional auto-unlock logic
        if (user.LastLockoutDate.HasValue &&
            DateTime.UtcNow > user.LastLockoutDate.Value.AddMinutes(LOCKOUT_MINUTES))
        {
            // Auto unlock
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

    // ❌ WRONG PASSWORD
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

    // ✅ SUCCESSFUL LOGIN – RESET COUNTERS
    user.FailedPasswordAttemptCount = 0;
    user.FailedPasswordAttemptWindowStart = null;
    user.IsLockedOut = false;
    user.LastLoginDate = DateTime.UtcNow;

    await context.SaveChangesAsync();

    // OTP logic (unchanged)
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

    UPDATE AppLogin
SET IsLockedOut = 0,
    FailedPasswordAttemptCount = 0,
    LastLockoutDate = NULL
WHERE UserId = 'testuser';

    
    
    
    [HttpPost]
    public async Task<IActionResult> Login([FromBody] AppLogin login)
    {
        string serverCaptcha = HttpContext.Session.GetString("CaptchaCode");

        if (serverCaptcha == null || login.Captcha?.ToLower() != serverCaptcha.ToLower())
            return Json(new { success = false, message = "Invalid Captcha" });

        var user = await context.AppLogins
            .FirstOrDefaultAsync(x => x.UserId == login.UserId);

        if (user == null)

            return Json(new 
            {
              success = false, 
              message = "User not found" 
            
            });

        bool isPasswordValid = hash_Password.VerifyPassword(login.Password, user.Password, user.PasswordSalt);

        if (!isPasswordValid)
            return Json(new 
            { 
                success = false, 
              message = "Incorrect password" 
            
            });

       
        string otp = new Random().Next(100000, 999999).ToString();

        HttpContext.Session.SetString("LoginOTP", otp);
        HttpContext.Session.SetString("OTPUser", login.UserId);
        HttpContext.Session.SetString("OTPExpiry",
DateTime.UtcNow.AddMinutes(5).ToString("O"));


        string smsError;
        bool smsSent = SendSmsToUser(login.UserId, otp, out smsError);

        if (!smsSent)
        {
            return Json(new
            {
                success = false,
                message = smsError
            });
        }

        return Json(new { success = true, otpRequired = true });
    }



 public partial class AppLogin
 {
     public Guid Id { get; set; }
     public string UserId { get; set; } = null!;
     [NotMapped]
     public string? Captcha { get; set; }

     public string Password { get; set; } = null!;
     [NotMapped]
     public string ConfirmPassword { get; set; } = null!;
     [NotMapped]
     public string NewPassword { get; set; } = null!;

     public int? PasswordFormat { get; set; }
     public string? PasswordSalt { get; set; }
     public string? MobilePin { get; set; }
     public string? Email { get; set; }
     public string? LoweredEmail { get; set; }
     public string? PasswordQuestion { get; set; }
     public string? PasswordAnswer { get; set; }
     public bool? IsApproved { get; set; }
     public bool? IsLockedOut { get; set; }
     public DateTime? CreateDate { get; set; }
     public DateTime? LastLoginDate { get; set; }
     public DateTime? LastPasswordChangedDate { get; set; }
     public DateTime? LastLockoutDate { get; set; }
     public int? FailedPasswordAttemptCount { get; set; }
     public DateTime? FailedPasswordAttemptWindowStart { get; set; }
     public int? FailedPasswordAnswerAttemptCount { get; set; }
     public DateTime? FailedPasswordAnswerAttemptWindowStart { get; set; }
     public string? Comment { get; set; }
 }
