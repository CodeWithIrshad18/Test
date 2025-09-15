<li>
    <a href="/AttachmentDownloadHandler?file=@WebUtility.UrlEncode(fileName)" target="_blank">@cleanFileName</a>
</li>



@using System.Net

<li>
    <a href="/AttachmentDownloadHandler?file=@Url.Encode(fileName)" target="_blank">@cleanFileName</a>
</li>

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

    // Decode file name back from URL encoding
    var decodedFile = Uri.UnescapeDataString(file);

    var uploadPath = configuration["FileUpload:Path"];
    var filePath = Path.Combine(uploadPath, decodedFile);

    if (!System.IO.File.Exists(filePath))
    {
        return Content("File not found.");
    }

    var stream = new FileStream(filePath, FileMode.Open, FileAccess.Read);
    var contentType = GetContentType(filePath);
    return File(stream, contentType, Path.GetFileName(filePath));
}



var cleanFileName = ExtractFileName(fileName);

<li>
				<a href="/AttachmentDownloadHandler?file=@fileName" target="_blank">@cleanFileName</a>
</li>
