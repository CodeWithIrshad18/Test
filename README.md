@{
    var pnoList = ViewBag.PnoEnameList as List<dynamic>; // or List<AppEmployeeMaster> if available
    var currentPno = ViewBag.Pno as string;
    bool isExistingUser = pnoList != null && pnoList.Any(e => e.Pno == currentPno);
}
@if ((bool)ViewBag.UploadLimitReached && !isExistingUser)
{
    <script>
        Swal.fire("Upload Blocked", "Upload limit of 20 reached. You are not allowed to upload.", "error");
        document.getElementById("video").style.display = "none";
        document.getElementById("captureBtn").style.display = "none";
        document.getElementById("retakeBtn").style.display = "none";
        document.getElementById("submitBtn").style.display = "none";
    </script>
}



int registeredCount = context.AppPeople.Select(p => p.Pno).Distinct().Count();
ViewBag.UploadLimitReached = registeredCount >= 20;

@if ((bool)ViewBag.UploadLimitReached && !ViewBag.PnoEnameList.Any(e => e.Pno == ViewBag.Pno))
{
    <script>
        Swal.fire("Upload Blocked", "Upload limit of 20 reached. You are not allowed to upload.", "error");
        document.getElementById("video").style.display = "none";
        document.getElementById("captureBtn").style.display = "none";
        document.getElementById("retakeBtn").style.display = "none";
        document.getElementById("submitBtn").style.display = "none";
    </script>
}



var existingPerson = await context.AppPeople.FirstOrDefaultAsync(p => p.Pno == Pno);

if (existingPerson != null)
{
    // ✅ Existing user – allow update
    existingPerson.Name = Name;
    existingPerson.Image = fileName;
    context.AppPeople.Update(existingPerson);
}
else
{
    // ✅ Count how many users already registered
    int registeredCount = await context.AppPeople.Select(p => p.Pno).Distinct().CountAsync();

    if (registeredCount >= 20)
    {
        // ❌ Don't allow new users after 20
        return BadRequest(new { success = false, message = "Upload limit reached. Only existing users can update their image." });
    }

    // ✅ Less than 20 – allow new entry
    var person = new AppPerson
    {
        Pno = Pno,
        Name = Name,
        Image = fileName
    };
    context.AppPeople.Add(person);
}






public IActionResult UploadImage()
{
    var pno = HttpContext.Request.Cookies["Session"];
    var userName = HttpContext.Request.Cookies["UserName"];

    ViewBag.Pno = pno;
    ViewBag.Name = userName;

    if (string.IsNullOrEmpty(pno) || string.IsNullOrEmpty(userName))
    {
        return RedirectToAction("Login", "User");
    }

    var PnoEnameList = context1.AppEmployeeMasters
            .Select(x => new
            {
                Pno = x.Pno,
                Ename = x.Ename,
            })
            .ToList();
    ViewBag.PnoEnameList = PnoEnameList;

    // ✅ Count how many unique users have uploaded an image
    int uploadedUserCount = context.AppPeople.Select(p => p.Pno).Distinct().Count();
    ViewBag.UploadedUserCount = uploadedUserCount;

    return View();
}

<script>
    document.addEventListener("DOMContentLoaded", function () {
        var uploadedCount = @Html.Raw(ViewBag.UploadedUserCount ?? 0);

        if (uploadedCount >= 20) {
            // Show warning
            Swal.fire({
                title: "Upload Limit Reached",
                text: "More than 20 users have already uploaded images. Uploading is disabled.",
                icon: "warning"
            });

            // Hide camera and related controls
            document.getElementById("video").style.display = "none";
            document.getElementById("captureBtn").style.display = "none";
            document.getElementById("retakeBtn").style.display = "none";
            document.getElementById("submitBtn").style.display = "none";
        }
    });
</script>




this is my controller 

public IActionResult UploadImage()
{
    var pno = HttpContext.Request.Cookies["Session"];
    var userName = HttpContext.Request.Cookies["UserName"];

    ViewBag.Pno = pno;
    ViewBag.Name = userName;

    if (string.IsNullOrEmpty(pno) || string.IsNullOrEmpty(userName))
    {
        return RedirectToAction("Login","User");
    }

    var PnoEnameList = context1.AppEmployeeMasters
            .Select(x => new
            {
                Pno = x.Pno,
                Ename = x.Ename,
            })
            .ToList();
    ViewBag.PnoEnameList = PnoEnameList;


    return View();
}

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

and this is my view side 
<div class="card rounded-9">
   
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
        </fieldset>
       
    </div>
</div>



<script>
   
    navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } })
        .then(function (stream) {
            let video = document.querySelector("video");
            video.srcObject = stream;
            video.play();
        })
        .catch(function (error) {
            console.error("Error accessing camera: ", error);
        });

   


    document.getElementById("captureBtn").addEventListener("click", function () {
        let video = document.getElementById("video");
        let canvas = document.getElementById("canvas");
        let context = canvas.getContext("2d");

       
        canvas.width = video.videoWidth;
        canvas.height = video.videoHeight;
        context.drawImage(video, 0, 0, canvas.width, canvas.height);

        context.translate(canvas.width, 0);
        context.scale(-1, 1);
        context.drawImage(video, 0, 0, canvas.width, canvas.height);
        context.setTransform(1, 0, 0, 1, 0, 0);

       
        let imageData = canvas.toDataURL("image/png");
        document.getElementById("previewImage").src = imageData;
        document.getElementById("previewImage").style.display = "block";
        document.getElementById("photoData").value = imageData;

        
        video.style.display = "none";
        document.getElementById("captureBtn").style.display = "none";
        document.getElementById("retakeBtn").style.display = "inline-block";
        document.getElementById("submitBtn").disabled = false; 
    });

    
    document.getElementById("retakeBtn").addEventListener("click", function () {
        let video = document.getElementById("video");

        
        video.style.display = "block";
        document.getElementById("captureBtn").style.display = "inline-block";
        document.getElementById("retakeBtn").style.display = "none";
        document.getElementById("previewImage").style.display = "none";
        document.getElementById("submitBtn").disabled = true; 
    });

    
</script>

<script>
   
    var pnoEnameList = @Html.Raw(JsonConvert.SerializeObject(ViewBag.PnoEnameList));


    document.addEventListener("DOMContentLoaded", function () {
        document.getElementById("Pno").addEventListener("input", function () {
            var pno = this.value;


            var user = pnoEnameList.find(u => u.Pno === pno);

            if (user) {
                document.getElementById("Name").value = user.Ename;

            } else {
                document.getElementById("Name").value = "";

            }



        });
    });

</script>


<script>
    document.getElementById('form2').addEventListener('submit', function (event) {
        event.preventDefault();

        var isValid = true;
        var form = this;
        var elements = form.querySelectorAll('input, select, textarea');

        elements.forEach(function (element) {
            if (['ApprovalFile'].includes(element.id)) {
                return;
            }

            if (element.value.trim() === '') {
                isValid = false;
                element.classList.add('is-invalid');
            } else {
                element.classList.remove('is-invalid');
            }
        });

        if (isValid) {
          
            Swal.fire({
                title: "Uploading...",
                text: "Please wait while your image is being uploaded.",
                didOpen: () => {
                    Swal.showLoading();
                },
                allowOutsideClick: false,
                allowEscapeKey: false
            });

          
            const formData = new FormData(form);

            fetch(form.action, {
                method: 'POST',
                body: formData
            })
                .then(response => {
                    if (response.redirected) {
                        window.location.href = response.url; 
                    } else if (response.ok) {
                        Swal.fire({
                            title: "Success!",
                            text: "Data Saved Successfully",
                            icon: "success",
                            confirmButtonText: "OK"
                        });
                    }
                    
                    else {
                        throw new Error("Upload failed.");
                    }
                })
                .catch(error => {
                    Swal.fire("Error", "There was an error uploading the image: " + error.message, "error");
                });
        }
    });
</script>


in this i want that fetch count of user that uploads image if it is more than 20 then show a message and dont take pictures and hide video and buttons
