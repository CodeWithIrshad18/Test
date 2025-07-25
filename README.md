this is controller method and i dont want to save image also it shows error but save the images
 [HttpPost]
 [ValidateAntiForgeryToken]
 public async Task<IActionResult> UploadImage(string Pno, string Name, string photoData)
 {
     if (!string.IsNullOrEmpty(photoData) && !string.IsNullOrEmpty(Pno) && !string.IsNullOrEmpty(Name))
     {
         try
         {
             byte[] imageBytes = Convert.FromBase64String(photoData.Split(',')[1]);

             string folderPath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/Images");

             if (!Directory.Exists(folderPath))
             {
                 Directory.CreateDirectory(folderPath);
             }

             string fileName = $"{Pno}-{Name}.jpg";
             string filePath = Path.Combine(folderPath, fileName);

             System.IO.File.WriteAllBytes(filePath, imageBytes);

             var existingPerson = await context.AppPeople.FirstOrDefaultAsync(p => p.Pno == Pno);

             if (existingPerson != null)
             {
                
                 existingPerson.Name = Name;
                 existingPerson.Image = fileName;
                 context.AppPeople.Update(existingPerson);
             }

             else
             {
                 
                 int registeredCount = await context.AppPeople.Select(p => p.Pno).Distinct().CountAsync();

                 if (registeredCount >= 20)
                 {
                     
                     return BadRequest(new { success = false, message = "Upload limit reached to 20. Only existing users can update their image." });
                 }

                 
                 var person = new AppPerson
                 {
                     Pno = Pno,
                     Name = Name,
                     Image = fileName
                 };
                 context.AppPeople.Add(person);
             }

             await context.SaveChangesAsync();

             return Ok(new { success = true, message = "Image uploaded and data saved successfully." });
         }
         catch (Exception ex)
         {
             return StatusCode(500, new { success = false, message = "Error saving image: " + ex.Message });
         }
     }

     return BadRequest(new { success = false, message = "Missing required fields!" });
 }

