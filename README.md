this is my controller method to upload base image , I want to authenticate when user wants to change the base image and after enter of password then it changes the image , show a popup to user input of password then authenticate

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

this is my view side 
 <div class="card-header text-center" style="background-color: #bbb8bf;color: #000000;font-weight:bold;">
     Capture Photo
 </div>
 <div class="col-md-12">
     <fieldset style="border:1px solid #bfbebe;padding:5px 20px 5px 20px;border-radius:6px;">
         <div class="row">
             <form asp-action="UploadImage" method="post" id="form2">
                 <div class="form-group row">
                     <div class="col-sm-1">
                         <label>Pno</label>
                     </div>
                     <div class="col-sm-3">
                         <input id="Pno" name="Pno" class="form-control" type="number" value="@ViewBag.Pno" oninput="javascript: if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);" maxlength="6" autocomplete="off" />
                     </div>
                     <div class="col-sm-1">
                         <label>Name</label>
                     </div>
                     <div class="col-sm-3">
                         <input id="Name" name="Name" class="form-control" value="@ViewBag.Name" />
                     </div>
                     <div class="col-sm-1">
                         <label>Capture Photo</label>
                     </div>
                     <div class="col-sm-3">
                         <video id="video" width="320" height="240" autoplay playsinline></video>
                         <canvas id="canvas" style="display:none;"></canvas>

                       
                         <img id="previewImage" src="" alt="Captured Image" style="width: 200px; display: none; border: 2px solid black; margin-top: 5px;" />

                        
                         <button type="button" id="captureBtn" class="btn btn-primary">Capture</button>
                         <button type="button" id="retakeBtn" class="btn btn-danger" style="display: none;">Retake</button>
                         <a asp-action="GeoFencing" asp-controller="Geo" class="control-label btn btn-warning"><i class="fa fa-arrow-left" aria-hidden="true" style="font-size:16px;"></i>&nbsp;&nbsp;Back</a>
                        
                         <input type="hidden" id="photoData" name="photoData" />
                     </div>
                 </div>

                 <button type="submit" class="btn btn-success" id="submitBtn" disabled>Save Details</button>
             </form>
             
         </div>
