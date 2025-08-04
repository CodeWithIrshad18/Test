I have this full code make changes to this 
 [HttpPost]
 public async Task<IActionResult> Login(AppLogin login)
 {

     if (!string.IsNullOrEmpty(login.UserId) && string.IsNullOrEmpty(login.Password))
     {
         ViewBag.FailedMsg = "Login Failed: Password is required";
         return View(login);
     }

     var user = await context.AppLogins
         .Where(x => x.UserId == login.UserId)
         .FirstOrDefaultAsync();

     if (user != null)
     {

         bool isPasswordValid = hash_Password.VerifyPassword(login.Password, user.Password, user.PasswordSalt);

         if (isPasswordValid)
         {


             string query = @"SELECT EMA_PERNO, EMA_ENAME 
          FROM SAPHRDB.dbo.TEmplAli 
          WHERE EMA_PERNO = @Pno";

             var parameters = new { Pno = loginUserId };

             // Use Dapper to fetch the data
             EmpDto userLoginData;

             using (var connection = GetRFIDConnectionString())
             {
                 await connection.OpenAsync();
                 userLoginData = await connection.QueryFirstOrDefaultAsync<EmpDto>(query, parameters);
             }


             string userName = userLoginData?.EMA_ENAME ?? "Guest";

             HttpContext.Session.SetString("Session", userLoginData?.EMA_PERNO ?? "N/A");
             HttpContext.Session.SetString("UserName", userName);
             HttpContext.Session.SetString("UserSession", userLoginData.EMA_PERNO);


             //var UserLoginData = await context2.AppEmplMasters.
             //    Where(x => x.Pno == login.UserId).FirstOrDefaultAsync();

             //string userName = UserLoginData?.Ename ?? "Guest";



             //HttpContext.Session.SetString("Session", UserLoginData?.Pno ?? "N/A");
             //HttpContext.Session.SetString("UserName", UserLoginData?.Ename ?? "Guest");
             //HttpContext.Session.SetString("UserSession", login.UserId);

             //store cookies

             var cookieOptions = new CookieOptions
             {
                 Expires = DateTimeOffset.Now.AddYears(1),
                 HttpOnly = false,
                 Secure = true,
                 IsEssential = true
             };

             Response.Cookies.Append("UserSession", login.UserId, cookieOptions);
             Response.Cookies.Append("Session", userLoginData?.EMA_PERNO ?? "N/A", cookieOptions);
             Response.Cookies.Append("UserName", userLoginData?.EMA_ENAME ?? "Guest", cookieOptions);







             return RedirectToAction("GeoFencing", "Geo");

         }
         else
         {
             ViewBag.FailedMsg = "Login Failed: Incorrect password";
         }
     }
     else
     {
         ViewBag.FailedMsg = "Login Failed: User not found";
     }

     return View(login);
 }
