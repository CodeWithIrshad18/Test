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



                   return RedirectToAction("GeoFencing", "Face");
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

<div class="container">
                         <div class="col-lg-12 d-flex justify-content-center order-1 order-lg-0">
                      <div class="form-bg">
<form action="Login" class="form_main">

  
    <p class="heading text-center">  <img src="/AppImages/logo6.png" width="75%" height="30%;"></p>
    <div class="inputContainer">
        <svg class="inputIcon" width="16" height="16" fill="#2e2e2e" viewBox="0 0 16 16">
        <path d="M13.106 7.222c0-2.967-2.249-5.032-5.482-5.032-3.35 0-5.646 2.318-5.646 5.702 0 3.493 2.235 5.708 5.762 5.708.862 0 1.689-.123 2.304-.335v-.862c-.43.199-1.354.328-2.29.328-2.926 0-4.813-1.88-4.813-4.798 0-2.844 1.921-4.881 4.594-4.881 2.735 0 4.608 1.688 4.608 4.156 0 1.682-.554 2.769-1.416 2.769-.492 0-.772-.28-.772-.76V5.206H8.923v.834h-.11c-.266-.595-.881-.964-1.6-.964-1.4 0-2.378 1.162-2.378 2.823 0 1.737.957 2.906 2.379 2.906.8 0 1.415-.39 1.709-1.087h.11c.081.67.703 1.148 1.503 1.148 1.572 0 2.57-1.415 2.57-3.643zm-7.177.704c0-1.197.54-1.907 1.456-1.907.93 0 1.524.738 1.524 1.907S8.308 9.84 7.371 9.84c-.895 0-1.442-.725-1.442-1.914z"></path>
        </svg>
    <input asp-for="UserId" type="number" oninput="javascript: if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);" maxlength="6" class="inputField" id="ADID" placeholder="UserID" autocomplete="off">
    </div>
    
<div class="inputContainer">
    <svg class="inputIcon" width="16" height="16" fill="#2e2e2e" viewBox="0 0 16 16">
    <path d="M8 1a2 2 0 0 1 2 2v4H6V3a2 2 0 0 1 2-2zm3 6V3a3 3 0 0 0-6 0v4a2 2 0 0 0-2 2v5a2 2 0 0 0 2 2h6a2 2 0 0 0 2-2V9a2 2 0 0 0-2-2z"></path>
    </svg>
    <input asp-for="Password" type="password" type="password" class="inputField" id="password" placeholder="Password" autocomplete="off">
</div>
              
<div class="inputContainer captcha-box">
    <span id="captchaText"
          style="font-weight:bold;font-size:13px;letter-spacing:3px;background:#ffffff00;padding:6px 10px;border-radius:5px;"></span>

    <button type="button" onclick="generateCaptcha()" style="border:none;background:none;font-size:16px;">
        🔄
    </button>

    <input type="text" id="captchaInput" class="inputField" placeholder="Enter Captcha" style="padding-left:8px;font-size:11px;">
</div>

           
<button type="button" id="btnLogin">Submit</button>

</form>
</div>


   <script>
    function showLoading(show) {
        if (show) $("#loading-overlay").css("display", "flex");
        else $("#loading-overlay").hide();
    }

    $(document).on("keypress", function (e) {
        if (e.which === 13) {
            $("#btnLogin").click();
        }
    });

    let generatedCaptcha = "";

function generateCaptcha() {
    generatedCaptcha = Math.random().toString(36).substring(2, 8).toUpperCase();
    $("#captchaText").text(generatedCaptcha);
}


$(document).ready(function () {
    generateCaptcha();
});


    $("#btnLogin").click(function () {
        var adid = $("#ADID").val().trim();
        var password = $("#password").val().trim();
        var captchaEntered = $("#captchaInput").val().trim();

        if (!adid || !password) {
            Swal.fire({
                icon: 'warning',
                title: 'Missing Information',
                text: 'Please enter both UserId and password.',
                confirmButtonColor: '#3085d6'
            });
            return;
        }
        if (captchaEntered !== generatedCaptcha) {
        Swal.fire({
            icon: 'error',
            title: 'Invalid Captcha ❌',
            text: 'Captcha does not match. Please try again.'
        });

        generateCaptcha();
        $("#captchaInput").val("");
        return;
    }


        showLoading(true);

        $.ajax({
            url: '@Url.Action("Login", "User")',
            type: 'POST',
            contentType: 'application/json',
            data: JSON.stringify({ ADID: adid, Password: password }),
            success: function (response) {
                showLoading(false);

                if (response.success) {
                    Swal.fire({
                        title: '🎉 Welcome Back!',
                        html: '<img src="/images/img9.jpg" width="150">',
                        showConfirmButton: false,
                        timer: 3000,
                        background: '#f4f6f9',
                        backdrop: `
                            rgba(0,0,123,0.4)
                            url("/images/party.gif")
                            left top
                            no-repeat
                        `
                    });

                    setTimeout(function () {
                        window.location.href = '/TPR/Homepage';
                    }, 1800);
                } else {

                    Swal.fire({
                        title: 'Login Failed!',
                        html: '<img src="/AppImages/img14.gif" width="150">',
                        showConfirmButton: false,
                        timer: 3000,
                        background: '#f4f6f9',
                        backdrop: `
                            rgb(121 0 0 / 40%)                       
                            left top
                            no-repeat
                        `
                    });
                }
            },
            error: function () {
                showLoading(false);
                Swal.fire({
                    icon: 'error',
                    title: 'Server Error ⚠️',
                    text: 'Unable to contact server. Please try again later.'
                });
            }
        });
    });
</script>
