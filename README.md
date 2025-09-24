<script>
    const form = document.getElementById("form2");
    const passwordHidden = document.getElementById("PasswordHidden");
    const passwordInput = document.getElementById("PasswordInput");

    form.addEventListener("submit", function (e) {
        e.preventDefault();

        // quick validation
        let isValid = true;
        const elements = form.querySelectorAll('input, select, textarea');
        elements.forEach(el => {
            if (['ApprovalFile'].includes(el.id)) return;
            if (el.value.trim() === '') {
                isValid = false;
                el.classList.add('is-invalid');
            } else {
                el.classList.remove('is-invalid');
            }
        });
        if (!isValid) return;

        const pno = document.getElementById("Pno").value;

        fetch('/Geo/CheckIfExists?pno=' + pno)
            .then(res => res.json())
            .then(data => {
                if (data.exists) {
                    // Show password modal
                    $('#passwordModal').modal('show');

                    // Handle confirm click (attach only once)
                    document.getElementById("confirmPasswordBtn").onclick = function () {
                        passwordHidden.value = passwordInput.value;
                        $('#passwordModal').modal('hide');

                        submitWithSwal(); // final upload with Swal feedback
                    };
                } else {
                    // Direct submit if new record
                    submitWithSwal();
                }
            });
    });

    function submitWithSwal() {
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
            .then(async response => {
                const result = await response.json();

                if (response.ok && result.success) {
                    Swal.fire({
                        title: "Success!",
                        text: result.message || "Data Saved Successfully",
                        icon: "success",
                        confirmButtonText: "OK"
                    });
                } else {
                    Swal.fire("Error", result.message || "Upload failed.", "error");
                }
            })
            .catch(error => {
                Swal.fire("Error", "There was an error uploading the image: " + error.message, "error");
            });
    }
</script>





using System.Security.Cryptography;
using System.Text;

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

            // ✅ Hash entered password with stored salt
            if (!VerifyPassword(password, existingPerson2.Password, existingPerson2.PasswordSalt))
            {
                return Unauthorized(new { success = false, message = "Invalid password. Update denied." });
            }

            // Save new image
            byte[] imageBytes = Convert.FromBase64String(photoData.Split(',')[1]);
            string folderPath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/Images");
            if (!Directory.Exists(folderPath)) Directory.CreateDirectory(folderPath);

            string fileName = $"{Pno}-{Name}.jpg";
            string filePath = Path.Combine(folderPath, fileName);
            System.IO.File.WriteAllBytes(filePath, imageBytes);

            existingPerson.Name = Name;
            existingPerson.Image = fileName;

            context.AppPeople.Update(existingPerson);
        }
        else
        {
            // New person → allow save without password
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

/// <summary>
/// Verify entered password against stored hash + salt
/// </summary>
private bool VerifyPassword(string enteredPassword, string storedHash, string storedSalt)
{
    // Convert base64 salt back to bytes
    var saltBytes = Convert.FromBase64String(storedSalt);

    using (var sha256 = SHA256.Create())
    {
        var enteredBytes = Encoding.UTF8.GetBytes(enteredPassword);
        var combinedBytes = saltBytes.Concat(enteredBytes).ToArray();

        var hashBytes = sha256.ComputeHash(combinedBytes);
        var hashString = Convert.ToBase64String(hashBytes);

        return hashString == storedHash;
    }
}


document.getElementById("form2").addEventListener("submit", function (e) {
    e.preventDefault(); // stop default form submit
    var form = this;
    var pno = document.getElementById("Pno").value;

    fetch('/Geo/CheckIfExists?pno=' + pno)
        .then(res => res.json())
        .then(data => {
            if (data.exists) {
                // ✅ Show password modal if record exists
                $('#passwordModal').modal('show');
            } else {
                // ✅ Submit directly for new record
                submitForm(form);
            }
        });
});

// Confirm password → put value into hidden input → then call submit
document.getElementById("confirmPasswordBtn").addEventListener("click", function () {
    var enteredPassword = document.getElementById("PasswordInput").value;
    document.getElementById("PasswordHidden").value = enteredPassword;

    $('#passwordModal').modal('hide');
    submitForm(document.getElementById("form2"));
});

// Common function to handle form submission with Swal
function submitForm(form) {
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
            if (response.ok) {
                Swal.fire({
                    title: "Success!",
                    text: "Data Saved Successfully",
                    icon: "success",
                    confirmButtonText: "OK"
                });
            } else if (response.status === 401) {
                Swal.fire("Unauthorized", "Invalid password, update denied.", "error");
            } else {
                throw new Error("Upload failed.");
            }
        })
        .catch(error => {
            Swal.fire("Error", "There was an error uploading the image: " + error.message, "error");
        });
}





this is my full code 

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

            // ✅ Authenticate before updating
            if (string.IsNullOrEmpty(password))
            {
                return Unauthorized(new { success = false, message = "Password required to update image." });
            }

            // simple compare (for demo) – replace with hashed password check
            if (existingPerson2.Password != password)
            {
                return Unauthorized(new { success = false, message = "Invalid password. Update denied." });
            }

            // Save new image
            byte[] imageBytes = Convert.FromBase64String(photoData.Split(',')[1]);
            string folderPath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/Images");
            if (!Directory.Exists(folderPath)) Directory.CreateDirectory(folderPath);

            string fileName = $"{Pno}-{Name}.jpg";
            string filePath = Path.Combine(folderPath, fileName);
            System.IO.File.WriteAllBytes(filePath, imageBytes);

            existingPerson.Name = Name;
            existingPerson.Image = fileName;

            context.AppPeople.Update(existingPerson);
        }
        else
        {
            // New person → allow save without password
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

<style>
    

    video {
        transform: scaleX(-1);
        -webkit-transform: scaleX(-1); 
        -moz-transform: scaleX(-1); 
    }

</style>

<input type="hidden" id="PasswordHidden" name="password" />

<!-- Bootstrap Password Modal -->
<div class="modal fade" id="passwordModal" tabindex="-1" role="dialog">
  <div class="modal-dialog modal-dialog-centered">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title">Enter Password</h5>
        <button type="button" class="close" data-dismiss="modal">&times;</button>
      </div>
      <div class="modal-body">
        <input type="password" id="PasswordInput" class="form-control" placeholder="Enter your password" />
      </div>
      <div class="modal-footer">
        <button type="button" id="confirmPasswordBtn" class="btn btn-success">Confirm</button>
      </div>
    </div>
  </div>
</div>

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
            // Show loading
            Swal.fire({
                title: "Uploading...",
                text: "Please wait while your image is being uploaded.",
                didOpen: () => {
                    Swal.showLoading();
                },
                allowOutsideClick: false,
                allowEscapeKey: false
            });

            // Prepare form data
            const formData = new FormData(form);

            fetch(form.action, {
                method: 'POST',
                body: formData
            })
                .then(response => {
                    if (response.redirected) {
                        window.location.href = response.url; // handle redirect if needed
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


 <script>

     document.getElementById("form2").addEventListener("submit", function (e) {
    e.preventDefault(); // stop default form submit

    var pno = document.getElementById("Pno").value;

    fetch('/Geo/CheckIfExists?pno=' + pno)
        .then(res => res.json())
        .then(data => {
            if (data.exists) {
                // Show password modal if record exists
                $('#passwordModal').modal('show');
            } else {
                // Submit form directly (new record)
                e.target.submit();
            }
        });
});

// Confirm password → put value into hidden input → submit
document.getElementById("confirmPasswordBtn").addEventListener("click", function () {
    var enteredPassword = document.getElementById("PasswordInput").value;
    document.getElementById("PasswordHidden").value = enteredPassword;

    $('#passwordModal').modal('hide');
    document.getElementById("form2").submit();
});

 </script>

and this is my login model to authenticate 

public partial class AppLogin
{
    public Guid Id { get; set; }
    public string UserId { get; set; } = null!;
    public string Password { get; set; } = null!;
    [NotMapped]
    public string ConfirmPassword { get; set; } = null!;
    [NotMapped]
    public string NewPassword { get; set; } = null!;

    public int? PasswordFormat { get; set; }
    public string? PasswordSalt { get; set; }
}
