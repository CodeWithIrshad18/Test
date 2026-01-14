        public IActionResult Login()
        {
            string captcha = GenerateCaptchaText();
            HttpContext.Session.SetString("CaptchaCode", captcha);

            ViewBag.Captcha = captcha;

            return View();
        }

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

                
                HttpContext.Session.SetString("Session", userPno);
                HttpContext.Session.SetString("UserName", userName);
                HttpContext.Session.SetString("UserSession", appLogin.ADID);


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

                return Json(new { success = true });
            }
        }



 public async Task SendEmailAsync(string toEmail, string ccEmail, string bccEmail, string subject, string message)
 {
     var emailSettings = configuration.GetSection("EmailSettings");
     var email = new MimeMessage();
     email.From.Add(new MailboxAddress(emailSettings["SenderName"], emailSettings["SenderEmail"]));
     email.To.Add(new MailboxAddress(toEmail, toEmail));

     if (!string.IsNullOrEmpty(ccEmail))
     {
         email.Cc.Add(new MailboxAddress(ccEmail, ccEmail));
     }
     if (!string.IsNullOrEmpty(bccEmail))
     {
         email.Bcc.Add(new MailboxAddress(bccEmail, bccEmail));
     }
     email.Subject = subject;
     email.Body = new TextPart(TextFormat.Html)
     {
         Text = message
     };

     using (var smtp = new SmtpClient())
     {
         smtp.Connect(emailSettings["SMTPServer"], int.Parse(emailSettings["SMTPPort"]), MailKit.Security.SecureSocketOptions.None);
         await smtp.SendAsync(email);
         smtp.Disconnect(true);
     }
 }


SELECT EMA_PERNO, EMA_ENAME FROM SAPHRDB.dbo.T_Empl_All WHERE EMA_PERNO = @Pno
