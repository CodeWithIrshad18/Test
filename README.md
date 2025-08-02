[HttpPost]
public async Task<IActionResult> BulkHashPasswords()
{
    // Define the default password to assign
    string defaultPassword = "jusco@123";

    // Create password hasher instance
    var passwordHasher = new Hash_Password();
    var (hashedPassword, salt) = passwordHasher.HashPassword(defaultPassword);

    // Get all users that need password hashing (e.g., empty or unhashed passwords)
    var users = await context.AppLogins
        .Where(x => string.IsNullOrEmpty(x.Password) || x.Password.Length < 20) // or another condition
        .ToListAsync();

    foreach (var user in users)
    {
        user.Password = hashedPassword;
        user.PasswordSalt = salt;
    }

    await context.SaveChangesAsync();

    return Ok($"Updated {users.Count} user passwords successfully.");
}




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
