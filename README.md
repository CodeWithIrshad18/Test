i have this signup logic
 public IActionResult Signup()
        {
            return View();
        }
        [HttpPost]
        public async Task<IActionResult> Signup(AppLogin appLogin)
        {
            var existingUser = await context.AppLogins
                .Where(x => x.UserId == appLogin.UserId)
                .FirstOrDefaultAsync();

            if (existingUser != null)
            {
                ViewBag.DupData = "UserId is already Exist";
                return View(appLogin);
            }
            else
            {
                var passwordHasher = new Hash_Password();
                var (hashedPassword, salt) = hash_Password.HashPassword(appLogin.Password);

                appLogin.Password = hashedPassword;
                appLogin.PasswordSalt = salt;

                await context.AppLogins.AddAsync(appLogin);
                await context.SaveChangesAsync();

                ViewBag.Data = "Signup Successful";
                return RedirectToAction("Login");
            }
        }



i want bulk login hash Password for existing user for default password in App_Login jusco@123 
