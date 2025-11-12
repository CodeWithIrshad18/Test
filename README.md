MIT License

Copyright (c) 2018 Vincent Mühler

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.






<script>
    const form = document.getElementById("form2");
    const passwordHidden = document.getElementById("PasswordHidden");
    const passwordInput = document.getElementById("PasswordInput");
    const passwordModal = new bootstrap.Modal(document.getElementById('passwordModal'));

    form.addEventListener("submit", function (e) {
        e.preventDefault();
        const pno = document.getElementById("Pno").value;

        fetch('/TSUISLARS/Geo/CheckIfExists?pno=' + pno)
            .then(res => res.json())
            .then(data => {
                if (data.exists) {
                   
                    passwordInput.value = "";

                    
                    passwordModal.show();
                } else {
                   
                    submitForm(form);
                }
            });
    });

   
    document.getElementById("confirmPasswordBtn").addEventListener("click", function () {
        const enteredPassword = passwordInput.value.trim();

        if (!enteredPassword) {
            Swal.fire("Warning", "Please enter your password.", "warning");
            return;
        }

        passwordHidden.value = enteredPassword;

        
        passwordModal.hide();

      
        submitForm(form);
    });

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
            .then(async response => {
                const result = await response.json().catch(() => ({}));

                if (response.ok && result.success) {
                    Swal.fire({
                        title: "Success!",
                        text: result.message || "Data Saved Successfully",
                        icon: "success",
                        confirmButtonText: "OK"
                    }).then(() => {
                       
                        location.reload();
                    });
                } else if (response.status === 401) {
                    Swal.fire("Unauthorized", result.message || "Invalid password,Try Again", "error");
                } else {
                    Swal.fire("Error", result.message || "Upload failed.", "error");
                }
            })
            .catch(error => {
                Swal.fire("Error", "There was an error uploading the image: " + error.message, "error");
            });
    }
</script>



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
