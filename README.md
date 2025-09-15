this is my actual file name : 3a342b26-19e2-4e54-ade2-c3e86c108b07_21-07-2025_11-33-55_EFFECTIVE & SUSTAINABLE MOSQUITO CONTROL METHOD.pptx

this is name of file i am getting : 3a342b26-19e2-4e54-ade2-c3e86c108b07_21-07-2025_11-33-55_EFFECTIVE 

and this is my method and i am getting file not found error 

 [HttpGet("AttachmentDownloadHandler")]
 public IActionResult DownloadHandler([FromQuery] string file)
 {
     
     var user = HttpContext.Session.GetString("Session");
     if (string.IsNullOrEmpty(user))
     {
         return Content("Session expired or user not logged in.");
     }

     if (string.IsNullOrEmpty(file))
     {
         return Content("File name not specified.");
     }

     var uploadPath = configuration["FileUpload:Path"];
     var filePath = Path.Combine(uploadPath, file);

     if (!System.IO.File.Exists(filePath))
     {
         return Content("File not found.");
     }

    
     var stream = new FileStream(filePath, FileMode.Open, FileAccess.Read);
     var contentType = GetContentType(filePath);
     return File(stream, contentType, Path.GetFileName(filePath));
 }

