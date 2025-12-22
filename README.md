        [HttpPost]
        public async Task<IActionResult> Login([FromBody] AppLogin login, string returnUrl = null)
        {

            if (string.IsNullOrEmpty(login.UserId) || string.IsNullOrEmpty(login.Password))
            {
                return Json(new { success = false, message = "UserId and Password are required" });
            }


            string serverCaptcha = HttpContext.Session.GetString("CaptchaCode");

            if (string.IsNullOrEmpty(serverCaptcha))
            {
                return Json(new { success = false, message = "Captcha expired" });
            }

            if (string.IsNullOrEmpty(login.Captcha) ||
                !login.Captcha.Equals(serverCaptcha, StringComparison.OrdinalIgnoreCase))
            {
                return Json(new { success = false, message = "Invalid Captcha" });
            }


            var user = await context.AppLogins
                .FirstOrDefaultAsync(x => x.UserId == login.UserId);

            if (user == null)
            {
                return Json(new { success = false, message = "User not found" });
            }

            bool isPasswordValid = hash_Password.VerifyPassword(
                login.Password,
                user.Password,
                user.PasswordSalt
            );

            if (!isPasswordValid)
            {
                return Json(new { success = false, message = "Incorrect password" });
            }


            string query = @"
        SELECT EMA_PERNO, EMA_ENAME
        FROM SAPHRDB.dbo.T_Empl_All
        WHERE EMA_PERNO = @Pno";

            EmpDTO userLoginData;

            using (var connection = GetRFIDConnectionString())
            {
                await connection.OpenAsync();
                userLoginData = await connection.QueryFirstOrDefaultAsync<EmpDTO>(
                    query,
                    new { Pno = login.UserId }
                );
            }

            string userName = userLoginData?.EMA_ENAME ?? "Guest";
            string userPno = userLoginData?.EMA_PERNO ?? login.UserId;

            HttpContext.Session.SetString("Session", userPno);
            HttpContext.Session.SetString("UserName", userName);

            var claims = new List<Claim>
    {
        new Claim(ClaimTypes.Name, userName),
        new Claim("UserId", userPno)
    };

            var identity = new ClaimsIdentity(
                claims,
                CookieAuthenticationDefaults.AuthenticationScheme
            );

            await HttpContext.SignInAsync(
                CookieAuthenticationDefaults.AuthenticationScheme,
                new ClaimsPrincipal(identity),
                new AuthenticationProperties { IsPersistent = true }
            );

            return Json(new
            {
                success = true,
                message = "Login successful"
            });
        }


 public partial class AppLogin
 {
     public Guid Id { get; set; }
     public string UserId { get; set; } = null!;
     public string Password { get; set; } = null!;
     [NotMapped]
     public string ConfirmPassword { get; set; } = null!;
     [NotMapped]
     public string NewPassword { get; set; } = null!;

     [NotMapped]
     public string? Captcha { get; set; }

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


"ConnectionStrings": {
  "SAPHRDB": "Server=10.0.168.50;Database=SAPHRDB;User Id=fs;Password=p@ssW0Rd321;TrustServerCertificate=yes",
  "PSDBCs": "Server=10.0.168.30;Database=PSDB;User Id=fs;Password=jusco@123;TrustServerCertificate=yes"
},

builder.Services.AddDbContext<PSDBContext>(item => item.UseSqlServer(Config.GetConnectionString("PSDBCs")));

why user is getting null
