[HttpPost]
public async Task<IActionResult> BulkHashPasswords()
{
    using var transaction = await context.Database.BeginTransactionAsync();
    try
    {
        var passwordHasher = new Hash_Password();

        // Get only users whose passwords are still in plain text
        var users = await context.AppLogins
            .Where(x => !string.IsNullOrEmpty(x.Password) && x.Password.Length < 20)
            .ToListAsync();

        foreach (var user in users)
        {
            // Keep original plain password before hashing
            var plainPassword = user.Password;

            var (hashedPassword, salt) = passwordHasher.HashPassword(plainPassword);

            user.Password = hashedPassword;
            user.PasswordSalt = salt;
        }

        await context.SaveChangesAsync();
        await transaction.CommitAsync();

        return Ok($"Updated {users.Count} user passwords successfully.");
    }
    catch (Exception ex)
    {
        await transaction.RollbackAsync();
        return StatusCode(500, $"Error updating passwords: {ex.Message}");
    }
}



userId  password

118202	06011966
118722	10031966
118868	16091965


this is my bulk hashing password i want to convert these above password to hash password not jusco@123

 [HttpPost]
 public async Task<IActionResult> BulkHashPasswords()
 {

     string defaultPassword = "jusco@123";


     var passwordHasher = new Hash_Password();
     var (hashedPassword, salt) = passwordHasher.HashPassword(defaultPassword);


     var users = await context.AppLogins
         .Where(x => string.IsNullOrEmpty(x.Password) || x.Password.Length < 20)
         .ToListAsync();

     foreach (var user in users)
     {
         user.Password = hashedPassword;
         user.PasswordSalt = salt;
     }

     await context.SaveChangesAsync();

     return Ok($"Updated {users.Count} user passwords successfully.");
 }
