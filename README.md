[HttpPost]
public async Task<IActionResult> Login([FromBody] AppLogin appLogin)
{
    if (appLogin == null || string.IsNullOrEmpty(appLogin.ADID) || string.IsNullOrEmpty(appLogin.Password))
        return Json(new { success = false, message = "Missing credentials" });

    string apiUrl = "https://services.tsuisl.co.in/ADService/API/ADID/ADService";

    using (var client = new HttpClient())
    {
        client.DefaultRequestHeaders.Add("XApiKey", "CityGis#123");

        var json = JsonConvert.SerializeObject(new
        {
            Domain = "tatasteel",
            PNo = appLogin.ADID,
            Password = appLogin.Password
        });

        var content = new StringContent(json, System.Text.Encoding.UTF8, "application/json");
        var response = await client.PostAsync(apiUrl, content);

        if (!response.IsSuccessStatusCode)
            return Json(new { success = false, message = "API call failed" });

        var result = await response.Content.ReadAsStringAsync();

        bool.TryParse(result, out bool apiResult);

        if (!apiResult)
            return Json(new { success = false, message = "Invalid login" });

        // ✅ User authenticated via external API, now load details
        string query = @"SELECT EMA_PERNO, EMA_ENAME FROM SAPHRDB.dbo.T_Empl_All WHERE EMA_PERNO = @Pno";
        var parameters = new { Pno = appLogin.ADID };

        EmpDTO userLoginData;

        using (var connection = GetRFIDConnectionString())
        {
            await connection.OpenAsync();
            userLoginData = await connection.QueryFirstOrDefaultAsync<EmpDTO>(query, parameters);
        }

        string userName = userLoginData?.EMA_ENAME ?? "Guest";
        string userPno = userLoginData?.EMA_PERNO ?? "N/A";

        // ✅ Set session (optional)
        HttpContext.Session.SetString("Session", userPno);
        HttpContext.Session.SetString("UserName", userName);
        HttpContext.Session.SetString("UserSession", appLogin.ADID);

        // ✅ Sign in user (the missing part)
        var claims = new List<Claim>
        {
            new Claim(ClaimTypes.NameIdentifier, userPno),
            new Claim(ClaimTypes.Name, userName)
        };

        var identity = new ClaimsIdentity(claims, CookieAuthenticationDefaults.AuthenticationScheme);
        var principal = new ClaimsPrincipal(identity);

        await HttpContext.SignInAsync(CookieAuthenticationDefaults.AuthenticationScheme, principal);

        return Json(new { success = true });
    }
}



this is my login logic 

  [HttpPost]
  public async Task<IActionResult> Login([FromBody] AppLogin appLogin)
  {
      if (appLogin == null || string.IsNullOrEmpty(appLogin.ADID) || string.IsNullOrEmpty(appLogin.Password))
          return Json(new { success = false, message = "Missing credentials" });

      string apiUrl = "https://services.tsuisl.co.in/ADService/API/ADID/ADService";

      using (var client = new HttpClient())
      {
          client.DefaultRequestHeaders.Add("XApiKey", "CityGis#123");

          var json = JsonConvert.SerializeObject(new
          {
              Domain = "tatasteel",
              PNo = appLogin.ADID,
              Password = appLogin.Password
          });

          var content = new StringContent(json, System.Text.Encoding.UTF8, "application/json");

          var response = await client.PostAsync(apiUrl, content);
          if (!response.IsSuccessStatusCode)
              return Json(new { success = false, message = "API call failed" });

          var result = await response.Content.ReadAsStringAsync();

          bool apiResult = false;
          bool.TryParse(result, out apiResult);

          if (apiResult)
          {
              string query = @"
          SELECT EMA_PERNO, EMA_ENAME 
          FROM SAPHRDB.dbo.T_Empl_All 
          WHERE EMA_PERNO = @Pno";

              var parameters = new { Pno = appLogin.ADID };

              EmpDTO userLoginData;

              using (var connection = GetRFIDConnectionString())
              {
                  await connection.OpenAsync();
                  userLoginData = await connection.QueryFirstOrDefaultAsync<EmpDTO>(query, parameters);
              }

              string userName = userLoginData?.EMA_ENAME ?? "Guest";
              string userPno = userLoginData?.EMA_PERNO ?? "N/A";


              HttpContext.Session.SetString("Session", userPno);
              HttpContext.Session.SetString("UserName", userName);
              HttpContext.Session.SetString("UserSession", appLogin.ADID);

              return Json(new { success = true });


            
          }

          else
          {
              return Json(new { success = false, message = "Invalid login" });
          }
             
      }
  }

  and this is in my program.cs
  builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.LoginPath = "/User/Login";
        options.AccessDeniedPath = "/TPR/AccessDenied";
    });

builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("CanWrite", policy => policy.Requirements.Add(new PermissionRequirement("AllowWrite")));
    options.AddPolicy("CanRead", policy => policy.Requirements.Add(new PermissionRequirement("AllowRead")));
    options.AddPolicy("CanModify", policy => policy.Requirements.Add(new PermissionRequirement("AllowModify")));
    options.AddPolicy("CanDelete", policy => policy.Requirements.Add(new PermissionRequirement("AllowDelete")));
});
builder.Services.AddTransient<IAuthorizationHandler, PermissionHandler>();
