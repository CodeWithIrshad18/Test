{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "EmailSettings": {
    "SMTPServer": "144.0.11.253",
    "SMTPPort": 25,
    "SenderEmail": "automatic_mail@tatasteel.com",
    "SenderName": "TSUISL"
  },
  "ConnectionStrings": {
    "SAPHRDB": "Server=10.0.168.50;Database=SAPHRDB;User Id=fs;Password=p@ssW0Rd321;TrustServerCertificate=yes",
    "TEHPDB": "Server=10.0.168.50;Database=TEHPDB;User Id=fs;Password=p@ssW0Rd321;TrustServerCertificate=yes;"
    //"TEHP": "Server=10.0.168.30;Database=TEHPDB;User Id=fs;Password=jusco@123;TrustServerCertificate=true;"
  },
  "AllowedHosts": "*"
}



builder.Services.AddControllersWithViews();
var Provider = builder.Services.BuildServiceProvider();
var Config = Provider.GetRequiredService<IConfiguration>();
builder.Services.AddDbContext<TEHPDBContext>(item => item.UseSqlServer(Config.GetConnectionString("TEHPDB")));
builder.Services.AddTransient<Hash_Password>();
builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.LoginPath = "/Account/Login";
        options.AccessDeniedPath = "/PS/AccessDenied";
    });
builder.Services.AddSession(options =>
{
    options.IdleTimeout = TimeSpan.FromMinutes(30);
    options.Cookie.HttpOnly = true;
    options.Cookie.IsEssential = true;
});
var app = builder.Build();

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


        //var user = await context.AppLogins
        //    .FirstOrDefaultAsync(x => x.UserId == login.UserId);


        string query1 = @"
    SELECT * from App_Login where UserId = @Pno";

        AppLogin LoginData;

        using (var connection = GetConnectionString())
        {
            await connection.OpenAsync();
            LoginData = await connection.QueryFirstOrDefaultAsync<AppLogin>(
                query1,
                new { Pno = login.UserId }
            );
        }


        if (LoginData == null)
        {
            return Json(new { success = false, message = "User not found" });
        }

        bool isPasswordValid = hash_Password.VerifyPassword(
            login.Password,
            LoginData.Password,
            LoginData.PasswordSalt
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
            await connection.OpenAsync(); // Error on this line System.NullReferenceException: 'Object reference not set to an instance of an object.'
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
