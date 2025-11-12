[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> UploadImage(string Pno, string Name, string photoData, string password)
{
    if (string.IsNullOrEmpty(photoData) || string.IsNullOrEmpty(Pno) || string.IsNullOrEmpty(Name))
    {
        return BadRequest(new { success = false, message = "Missing required fields!" });
    }

    try
    {
        var existingPerson = await context.AppPeople.FirstOrDefaultAsync(p => p.Pno == Pno);

        if (existingPerson != null)
        {
            var existingPerson2 = await context.AppLogins.FirstOrDefaultAsync(p => p.UserId == Pno);

            if (existingPerson2 == null)
                return Unauthorized(new { success = false, message = "User not found in login table." });

            if (string.IsNullOrEmpty(password))
            {
                return Unauthorized(new { success = false, message = "Password required to update image." });
            }


            if (!hash_Password.VerifyPassword(password, existingPerson2.Password, existingPerson2.PasswordSalt))
            {
                return Unauthorized(new { success = false, message = "Invalid password,Try Again!" });
            }


            byte[] imageBytes = Convert.FromBase64String(photoData.Split(',')[1]);
            string folderPath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/Images");
            if (!Directory.Exists(folderPath)) Directory.CreateDirectory(folderPath);

            string fileName = $"{Pno}-{Name}.jpg";
            string filePath = Path.Combine(folderPath, fileName);
            System.IO.File.WriteAllBytes(filePath, imageBytes);

            existingPerson.Name = Name;
            existingPerson.Image = fileName;
            existingPerson.CreatedOn = DateTime.Now;

            context.AppPeople.Update(existingPerson);
        }
        else
        {

            byte[] imageBytes = Convert.FromBase64String(photoData.Split(',')[1]);
            string folderPath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/Images");
            if (!Directory.Exists(folderPath)) Directory.CreateDirectory(folderPath);

            string fileName = $"{Pno}-{Name}.jpg";
            string filePath = Path.Combine(folderPath, fileName);
            System.IO.File.WriteAllBytes(filePath, imageBytes);

            var person = new AppPerson
            {
                Pno = Pno,
                Name = Name,
                Image = fileName
            };
            context.AppPeople.Add(person);
        }

        await context.SaveChangesAsync();
        return Ok(new { success = true, message = "Image uploaded successfully." });

    }
    catch (Exception ex)
    {
        return StatusCode(500, new { success = false, message = "Error saving image: " + ex.Message });
    }
}
