I have this js for this 

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
and this is my login logic 

 [HttpPost]
 public async Task<IActionResult> Login(AppLogin login)
 {
     if (!string.IsNullOrEmpty(login.UserId) && string.IsNullOrEmpty(login.Password))
     {
         ViewBag.FailedMsg = "Login Failed: Password is required";
         return View(login);
     }

     var user = await context.AppLogins
         .Where(x => x.UserId == login.UserId)
         .FirstOrDefaultAsync();

     if (user != null)
     {
         bool isPasswordValid = hash_Password.VerifyPassword(login.Password, user.Password, user.PasswordSalt);

         if (isPasswordValid)
         {

             string query = @"
         SELECT EMA_PERNO, EMA_ENAME 
         FROM SAPHRDB.dbo.T_Empl_All 
         WHERE EMA_PERNO = @Pno";

             var parameters = new { Pno = login.UserId };

             EmpDTO userLoginData;

             using (var connection = GetRFIDConnectionString())
             {
                 await connection.OpenAsync();
                 userLoginData = await connection.QueryFirstOrDefaultAsync<EmpDTO>(query, parameters);
             }

             string userName = userLoginData?.EMA_ENAME ?? "Guest";
             string userPno = userLoginData?.EMA_PERNO ?? "N/A";


             HttpContext.Session.SetString("Session", userPno);
             HttpContext.Session.SetString("UserName", userName);
             HttpContext.Session.SetString("UserSession", login.UserId);

             // Set cookies
             var cookieOptions = new CookieOptions
             {
                 Expires = DateTimeOffset.Now.AddYears(1),
                 HttpOnly = false,
                 Secure = true,
                 IsEssential = true
             };

             Response.Cookies.Append("UserSession", login.UserId, cookieOptions);
             Response.Cookies.Append("Session", userPno, cookieOptions);
             Response.Cookies.Append("UserName", userName, cookieOptions);

             return RedirectToAction("GeoFencing", "Geo");
         }
         else
         {
             ViewBag.FailedMsg = "Login Failed: Incorrect password";
         }
     }
     else
     {
         ViewBag.FailedMsg = "Login Failed: User not found";
     }

     return View(login);


 }

provide based on these
