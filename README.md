  [HttpPost]
  public async Task<IActionResult> Login([FromBody] AppLogin appLogin)
  {
      if (appLogin == null || string.IsNullOrEmpty(appLogin.ADID) || string.IsNullOrEmpty(appLogin.Password))
          return Json(new { success = false, message = "Missing credentials" });

      string serverCaptcha = HttpContext.Session.GetString("CaptchaCode");

      if (serverCaptcha == null)
          return Json(new { success = false, message = "Captcha expired" });

      if (appLogin.Captcha == null || appLogin.Captcha.ToLower() != serverCaptcha.ToLower())
          return Json(new { success = false, message = "Invalid Captcha" });

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

          string query = @"SELECT EMA_PERNO, EMA_ENAME, EMA_EMAIL_ID 
                   FROM SAPHRDB.dbo.T_Empl_All 
                   WHERE EMA_PERNO = @Pno";

          var parameters = new { Pno = appLogin.ADID };
          EmpDTO userLoginData;

          using (var connection = GetRFIDConnectionString())
          {
              await connection.OpenAsync();
              userLoginData = await connection.QueryFirstOrDefaultAsync<EmpDTO>(query, parameters);
          }

          if (userLoginData == null)
              return Json(new { success = false, message = "User not found" });

          if (string.IsNullOrEmpty(userLoginData.EMA_EMAIL_ID))
              return Json(new { success = false, message = "Email not found" });

          string otp = GenerateOTP();

          HttpContext.Session.SetString("LoginOTP", otp);
          HttpContext.Session.SetString("OTP_ADID", appLogin.ADID);
          HttpContext.Session.SetString("OTP_UserName", userLoginData.EMA_ENAME);
          HttpContext.Session.SetString("OTP_Pno", userLoginData.EMA_PERNO);


          string subject = "TPR Login OTP";
          string body = $"<h3>Hello {userLoginData.EMA_ENAME}</h3><p>Your OTP for TPR is <b>{otp}</b></p><p>Valid for 5 minutes. Do not share with anyone.</p>";

          await emailService.SendEmailAsync(userLoginData.EMA_EMAIL_ID, null, null, subject, body);

          return Json(new { success = true, otpSent = true });
      }
  }


    [HttpPost]
    public async Task<IActionResult> VerifyOTP(string otp)
    {
        string sessionOtp = HttpContext.Session.GetString("LoginOTP");

        if (sessionOtp == null)
            return Json(new { success = false, message = "OTP expired" });

        if (otp != sessionOtp)
            return Json(new { success = false, message = "Invalid OTP" });


        string userName = HttpContext.Session.GetString("OTP_UserName");
        string userPno = HttpContext.Session.GetString("OTP_Pno");
        string adid = HttpContext.Session.GetString("OTP_ADID");



        HttpContext.Session.SetString("Session", userPno);
        HttpContext.Session.SetString("UserName", userName);
        HttpContext.Session.SetString("UserSession", adid);

        var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, userName),
    new Claim("UserId", userPno)
};

        var claimsIdentity = new ClaimsIdentity(claims, CookieAuthenticationDefaults.AuthenticationScheme);

        await HttpContext.SignInAsync(
            CookieAuthenticationDefaults.AuthenticationScheme,
            new ClaimsPrincipal(claimsIdentity),
            new AuthenticationProperties
            {
                IsPersistent = true
            });


        HttpContext.Session.Remove("LoginOTP");

        return Json(new { success = true });
    }
