i have this login method 
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
             var UserLoginData = await context.AppEmplMasters.
                 Where(x => x.Pno == login.UserId).FirstOrDefaultAsync();

             string userName = UserLoginData?.Ename ?? "Guest";



             HttpContext.Session.SetString("Session", UserLoginData?.Pno ?? "N/A");
             HttpContext.Session.SetString("UserName", UserLoginData?.Ename ?? "Guest");
             HttpContext.Session.SetString("UserSession",login.UserId);

             //store cookies

             var cookieOptions = new CookieOptions
             {
                 Expires = DateTimeOffset.Now.AddYears(1),
                 HttpOnly = false,
                 Secure = true,
                 IsEssential = true
             };

             Response.Cookies.Append("UserSession", login.UserId, cookieOptions);
             Response.Cookies.Append("Session", UserLoginData?.Pno ?? "N/A", cookieOptions);
             Response.Cookies.Append("UserName", UserLoginData?.Ename ?? "Guest", cookieOptions);






            
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


in this App_Login I have userid and password who is not encrypted in hashpassword 
i have userid 151514 and password is jusco@123 i want to do hashpassword using code like bulk pno and default password set to everyone for jusco@123
