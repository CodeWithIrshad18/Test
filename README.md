my frontend side make changes according to this 

<div id="content" role="main">

    <section id="home">
        <div class="hero-wrap">
            <div class="hero-mask opacity-8 bg-dark"></div>
            <div class="hero-bg parallax" style="background-image:url('@Url.Content("~/images/intro-bg.jpg")');"></div>
            <div class="hero-content d-flex fullscreen pt-5">
                <div class="container">
                    <div class="row">
                        <div class="col-lg-12 text-center order-1 order-lg-0">
                            <img src="~/Images/img7.png" style="width:18%;">
                          
                            <div class="typed-strings text-white fw-600 mb-0 text-uppercase h3">
                                <p>TPR Achievement through KPI</p>
                            </div>
                            
                            <h2 class="text-23 text-white fw-600 text-uppercase mb-0 ms-n1"><span class=""></span></h2>
                        </div>

                    </div>
                     <div class="row">
                         <div class="col-lg-12 d-flex justify-content-center order-1 order-lg-0">
                                                     <div class="form-bg">
<form action="" class="form_main">

  

    <p class="heading">Login</p>
    <div class="inputContainer">
        <svg class="inputIcon" xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="#2e2e2e" viewBox="0 0 16 16">
        <path d="M13.106 7.222c0-2.967-2.249-5.032-5.482-5.032-3.35 0-5.646 2.318-5.646 5.702 0 3.493 2.235 5.708 5.762 5.708.862 0 1.689-.123 2.304-.335v-.862c-.43.199-1.354.328-2.29.328-2.926 0-4.813-1.88-4.813-4.798 0-2.844 1.921-4.881 4.594-4.881 2.735 0 4.608 1.688 4.608 4.156 0 1.682-.554 2.769-1.416 2.769-.492 0-.772-.28-.772-.76V5.206H8.923v.834h-.11c-.266-.595-.881-.964-1.6-.964-1.4 0-2.378 1.162-2.378 2.823 0 1.737.957 2.906 2.379 2.906.8 0 1.415-.39 1.709-1.087h.11c.081.67.703 1.148 1.503 1.148 1.572 0 2.57-1.415 2.57-3.643zm-7.177.704c0-1.197.54-1.907 1.456-1.907.93 0 1.524.738 1.524 1.907S8.308 9.84 7.371 9.84c-.895 0-1.442-.725-1.442-1.914z"></path>
        </svg>
    <input type="text" class="inputField" id="ADID" placeholder="ADID" autocomplete="off">
    </div>
    
<div class="inputContainer">
    <svg class="inputIcon" xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="#2e2e2e" viewBox="0 0 16 16">
    <path d="M8 1a2 2 0 0 1 2 2v4H6V3a2 2 0 0 1 2-2zm3 6V3a3 3 0 0 0-6 0v4a2 2 0 0 0-2 2v5a2 2 0 0 0 2 2h6a2 2 0 0 0 2-2V9a2 2 0 0 0-2-2z"></path>
    </svg>
    <input type="password" class="inputField" id="password" placeholder="Password" autocomplete="off">
</div>

 <input type="hidden" id="serverCaptcha" value="@ViewBag.Captcha" />
    
<div class="inputContainer captcha-box">
   <div class="captcha-box2">
    <span id="captchaDisplay"></span>
    <div class="captcha-noise"></div>
</div>


<button type="button" id="refreshCaptchaBtn" onclick="refreshCaptcha()">
        <svg width="25" height="25" viewBox="0 0 24 24">
            <path fill="#333"
                d="M12 6V3L8 7l4 4V8c2.21 0 4 1.79 4 4s-1.79 
                   4-4 4-4-1.79-4-4H6c0 3.31 2.69 6 6 6s6-2.69 
                   6-6-2.69-6-6-6z" />
        </svg>
</button>


    <input type="text" id="captchaInput" class="inputField" placeholder="Enter Captcha" style="padding-left:8px;font-size:11px;">
</div>
              
<div id="loading-overlay">
        <div class="spinner-border text-primary" role="status">
            <span class="visually-hidden">Loading...</span>
        </div>
    </div>
           
<button type="button" id="btnLogin">Submit</button>

</form>
</div>
</div>
                         </div>
                         </div>

                </div>
               
            </div>
        </div>
    </section>

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

    function styleCaptcha(text) {
    const colors = ["#e74c3c","#2980b9","#27ae60","#8e44ad","#d35400","#2c3e50"];
    let html = "";

    for (let i = 0; i < text.length; i++) {
        let color = colors[Math.floor(Math.random() * colors.length)];
        let rotate = Math.floor(Math.random() * 20 - 10);

        html += `<span style="color:${color}; display:inline-block; transform:rotate(${rotate}deg);">
                    ${text[i]}
                 </span>`;
    }

    document.getElementById("captchaDisplay").innerHTML = html;
}

document.addEventListener("DOMContentLoaded", function () {
    styleCaptcha($("#serverCaptcha").val());
});

function refreshCaptcha() {
    fetch(window.appRoot+'User/RefreshCaptcha')
        .then(res => res.json())
        .then(data => {
            $("#serverCaptcha").val(data.captcha);
            styleCaptcha(data.captcha);
        });
}


    $("#btnLogin").click(function () {
        var adid = $("#ADID").val().trim();
        var password = $("#password").val().trim();
        var captchaEntered = $("#captchaInput").val().trim();
    var serverCaptcha = $("#serverCaptcha").val();

            if (captchaEntered.toLowerCase() !== serverCaptcha.toLowerCase()) {
        Swal.fire({
            icon: 'error',
            title: 'Invalid Captcha ❌',
            text: 'Captcha mismatch.'
        });

        refreshCaptcha();
        $("#captchaInput").val("");
        return;
    }

        if (!adid || !password) {
            Swal.fire({
                icon: 'warning',
                title: 'Missing Information',
                text: 'Please enter both ADID and password.',
                confirmButtonColor: '#3085d6'
            });
            return;
        }

        showLoading(true);

        $.ajax({
            url: '@Url.Action("Login", "User")',
            type: 'POST',
            contentType: 'application/json',
            data: JSON.stringify({ ADID: adid, Password: password , Captcha: captchaEntered}),
            success: function (response) {
                showLoading(false);

                if (response.success) {
                    Swal.fire({
                        title: '🎉 Welcome Back!',
                       html: `<img src="${window.appRoot}images/img9.jpg" width="150">`,
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
                        window.location.href = window.appRoot + 'TPR/Homepage';
                    }, 1800);
                } else {

                    Swal.fire({
                        title: 'Login Failed!',
                        html: `<img src="${window.appRoot}images/img14.gif" width="150">`,
                        showConfirmButton: false,
                        timer: 3000,
                        background: '#f4f6f9',
                        backdrop: `
                            rgb(121 0 0 / 40%)                       
                            left top
                            no-repeat
                        `
                    });

                      refreshCaptcha();
        $("#captchaInput").val("");
         $("#password").val("");
        return;
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

And modal like this

 <div class="modal fade" id="otpModal" tabindex="-1" data-bs-backdrop="static" data-bs-keyboard="false">
  <div class="modal-dialog modal-dialog-centered">
    <div class="modal-content">
      <div class="modal-header">         
        <h5 class="modal-title"><i class="fas fa-key"></i> Enter OTP</h5>

        <button type="button" class="btn-close" aria-label="Close" onclick="hideOtpModal()"></button>

      </div>
      <div class="modal-body">
       <input type="text"
       id="otpInput"
       class="form-control"
       placeholder="Enter 6-digit OTP"
       maxlength="6"
       inputmode="numeric"
       oninput="this.value=this.value.replace(/[^0-9]/g,'')" autocomplete="off"/>

      </div>
      <div class="modal-footer">
        <button class="btn btn-primary" id="verifyOtpBtn">Verify OTP</button>
      </div>
    </div>
  </div>
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

    function styleCaptcha(text) {
        const colors = ["#e74c3c", "#2980b9", "#27ae60", "#8e44ad", "#d35400", "#2c3e50"];
        let html = "";

        for (let i = 0; i < text.length; i++) {
            let color = colors[Math.floor(Math.random() * colors.length)];
            let rotate = Math.floor(Math.random() * 20 - 10);

            html += `<span style="color:${color}; display:inline-block; transform:rotate(${rotate}deg);">
                        ${text[i]}
                     </span>`;
        }

        document.getElementById("captchaDisplay").innerHTML = html;
    }

    document.addEventListener("DOMContentLoaded", function () {
        styleCaptcha($("#serverCaptcha").val());
    });

    function refreshCaptcha() {
        fetch(window.appRoot + 'User/RefreshCaptcha')
            .then(res => res.json())
            .then(data => {
                $("#serverCaptcha").val(data.captcha);
                styleCaptcha(data.captcha);
            });
    }

    // ---------------- LOGIN CLICK ----------------
    $("#btnLogin").click(function () {
        var adid = $("#ADID").val().trim();
        var password = $("#password").val().trim();
        var captchaEntered = $("#captchaInput").val().trim();
        var serverCaptcha = $("#serverCaptcha").val();

        if (captchaEntered.toLowerCase() !== serverCaptcha.toLowerCase()) {
            Swal.fire({
                icon: 'error',
                title: 'Invalid Captcha ❌',
                text: 'Captcha mismatch.'
            });

            refreshCaptcha();
            $("#captchaInput").val("");
            return;
        }

        if (!adid || !password) {
            Swal.fire({
                icon: 'warning',
                title: 'Missing Information',
                text: 'Please enter both ADID and password.'
            });
            return;
        }

        showLoading(true);

        $.ajax({
            url: '@Url.Action("Login", "User")',
            type: 'POST',
            contentType: 'application/json',
            data: JSON.stringify({ ADID: adid, Password: password, Captcha: captchaEntered }),
            success: function (response) {
                showLoading(false);

                if (response.success && response.otpSent) {

                    Swal.fire({
                        icon: 'success',
                        title: 'OTP Sent',
                        text: 'OTP has been sent to your registered email',
                        timer: 2000,
                        showConfirmButton: false
                    });

                    showOtpModal();
                }
                else {
                    Swal.fire({
                        title: 'Login Failed!',
                        html: `<img src="${window.appRoot}images/img14.gif" width="150">`,
                        showConfirmButton: false,
                        timer: 3000
                    });

                    refreshCaptcha();
                    $("#captchaInput").val("");
                    $("#password").val("");
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

    // ---------------- OTP MODAL ----------------
    function showOtpModal() {
        var modal = new bootstrap.Modal(document.getElementById('otpModal'));
        modal.show();
        $("#otpInput").val("").focus();
    }

    function hideOtpModal() {
        var modalEl = document.getElementById('otpModal');
        var modal = bootstrap.Modal.getInstance(modalEl);
        if (modal) modal.hide();
    }

    // ---------------- VERIFY OTP ----------------
    $("#verifyOtpBtn").click(function () {

        var otp = $("#otpInput").val().trim();

        if (otp.length !== 6) {
            Swal.fire({
                icon: 'warning',
                title: 'Invalid OTP',
                text: 'Please enter 6-digit OTP'
            });
            return;
        }

        showLoading(true);

        $.ajax({
            url: window.appRoot + 'User/VerifyOTP',
            type: 'POST',
            data: { otp: otp },
            success: function (response) {
                showLoading(false);

                if (response.success) {

                    Swal.fire({
                        title: '🎉 Login Successful!',
                        html: `<img src="${window.appRoot}images/img9.jpg" width="150">`,
                        showConfirmButton: false,
                        timer: 2000
                    });

                    hideOtpModal();

                    setTimeout(function () {
                        window.location.href = window.appRoot + 'TPR/Homepage';
                    }, 1500);
                }
                else {
                    Swal.fire({
                        icon: 'error',
                        title: 'OTP Failed',
                        text: response.message || 'Invalid OTP'
                    });
                }
            },
            error: function () {
                showLoading(false);
                Swal.fire({
                    icon: 'error',
                    title: 'Server Error',
                    text: 'Please try again'
                });
            }
        });
    });

</script>




public async Task SendEmailAsync(string toEmail, string ccEmail, string bccEmail, string subject, string message)
{
    var emailSettings = configuration.GetSection("EmailSettings");
    var email = new MimeMessage();
    email.From.Add(new MailboxAddress(emailSettings["SenderName"], emailSettings["SenderEmail"]));
    email.To.Add(new MailboxAddress(toEmail, toEmail));

    if (!string.IsNullOrEmpty(ccEmail))
    {
        email.Cc.Add(new MailboxAddress(ccEmail, ccEmail));
    }
    if (!string.IsNullOrEmpty(bccEmail))
    {
        email.Bcc.Add(new MailboxAddress(bccEmail, bccEmail));
    }
    email.Subject = subject;
    email.Body = new TextPart(TextFormat.Html)
    {
        Text = message
    };

    using (var smtp = new SmtpClient())
    {
        smtp.Connect(emailSettings["SMTPServer"], int.Parse(emailSettings["SMTPPort"]), MailKit.Security.SecureSocketOptions.None);
        await smtp.SendAsync(email);
        smtp.Disconnect(true);
    }
}

 [HttpPost]
 public async Task<IActionResult> Login([FromBody] AppLogin appLogin)
 {
     if (appLogin == null || string.IsNullOrEmpty(appLogin.ADID) || string.IsNullOrEmpty(appLogin.Password))
         return Json(new { success = false, message = "Missing credentials" });

     string serverCaptcha = HttpContext.Session.GetString("CaptchaCode");

     if (serverCaptcha == null)
         return Json(new { success = false, message = "Captcha expired" });

     if (appLogin.Captcha == null || appLogin.Captcha.ToLower() != serverCaptcha.ToLower())
         return Json(new { success = false, message = "Invalid Captcha" });

     string apiUrl = "https://services.tsuisl.co.in/ADService/API/ADID/ADService";

     using (var client = new HttpClient())
     {
         client.DefaultRequestHeaders.Add("XApiKey", "CityGis#123");

         var json = JsonConvert.SerializeObject(new
         {
             Domain = "tatasteel",
             PNo = appLogin.ADID,
             Password = appLogin.Password
         });

         var content = new StringContent(json, System.Text.Encoding.UTF8, "application/json");
         var response = await client.PostAsync(apiUrl, content);

         if (!response.IsSuccessStatusCode)
             return Json(new { success = false, message = "API call failed" });

         var result = await response.Content.ReadAsStringAsync();

         bool.TryParse(result, out bool apiResult);

         if (!apiResult)
             return Json(new { success = false, message = "Invalid login" });


         string query = @"SELECT EMA_PERNO, EMA_ENAME FROM SAPHRDB.dbo.T_Empl_All WHERE EMA_PERNO = @Pno";
         var parameters = new { Pno = appLogin.ADID };

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
         HttpContext.Session.SetString("UserSession", appLogin.ADID);


         var claims = new List<Claim>
 {
     new Claim(ClaimTypes.Name, userName),
     new Claim("UserId", userPno)
 };

         var claimsIdentity = new ClaimsIdentity(claims, CookieAuthenticationDefaults.AuthenticationScheme);

         await HttpContext.SignInAsync(
             CookieAuthenticationDefaults.AuthenticationScheme,
             new ClaimsPrincipal(claimsIdentity),
             new AuthenticationProperties
             {
                 IsPersistent = true
             });

         return Json(new { success = true });
     }
 }



<form action="" class="form_main">

  

    <p class="heading">Login</p>
    <div class="inputContainer">
        <svg class="inputIcon" xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="#2e2e2e" viewBox="0 0 16 16">
        <path d="M13.106 7.222c0-2.967-2.249-5.032-5.482-5.032-3.35 0-5.646 2.318-5.646 5.702 0 3.493 2.235 5.708 5.762 5.708.862 0 1.689-.123 2.304-.335v-.862c-.43.199-1.354.328-2.29.328-2.926 0-4.813-1.88-4.813-4.798 0-2.844 1.921-4.881 4.594-4.881 2.735 0 4.608 1.688 4.608 4.156 0 1.682-.554 2.769-1.416 2.769-.492 0-.772-.28-.772-.76V5.206H8.923v.834h-.11c-.266-.595-.881-.964-1.6-.964-1.4 0-2.378 1.162-2.378 2.823 0 1.737.957 2.906 2.379 2.906.8 0 1.415-.39 1.709-1.087h.11c.081.67.703 1.148 1.503 1.148 1.572 0 2.57-1.415 2.57-3.643zm-7.177.704c0-1.197.54-1.907 1.456-1.907.93 0 1.524.738 1.524 1.907S8.308 9.84 7.371 9.84c-.895 0-1.442-.725-1.442-1.914z"></path>
        </svg>
    <input type="text" class="inputField" id="ADID" placeholder="ADID" autocomplete="off">
    </div>
    
<div class="inputContainer">
    <svg class="inputIcon" xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="#2e2e2e" viewBox="0 0 16 16">
    <path d="M8 1a2 2 0 0 1 2 2v4H6V3a2 2 0 0 1 2-2zm3 6V3a3 3 0 0 0-6 0v4a2 2 0 0 0-2 2v5a2 2 0 0 0 2 2h6a2 2 0 0 0 2-2V9a2 2 0 0 0-2-2z"></path>
    </svg>
    <input type="password" class="inputField" id="password" placeholder="Password" autocomplete="off">
</div>

 <input type="hidden" id="serverCaptcha" value="@ViewBag.Captcha" />
    
<div class="inputContainer captcha-box">
   <div class="captcha-box2">
    <span id="captchaDisplay"></span>
    <div class="captcha-noise"></div>
</div>


<button type="button" id="refreshCaptchaBtn" onclick="refreshCaptcha()">
        <svg width="25" height="25" viewBox="0 0 24 24">
            <path fill="#333"
                d="M12 6V3L8 7l4 4V8c2.21 0 4 1.79 4 4s-1.79 
                   4-4 4-4-1.79-4-4H6c0 3.31 2.69 6 6 6s6-2.69 
                   6-6-2.69-6-6-6z" />
        </svg>
</button>


    <input type="text" id="captchaInput" class="inputField" placeholder="Enter Captcha" style="padding-left:8px;font-size:11px;">
</div>
              
<div id="loading-overlay">
        <div class="spinner-border text-primary" role="status">
            <span class="visually-hidden">Loading...</span>
        </div>
    </div>
           
<button type="button" id="btnLogin">Submit</button>

</form>



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

    function styleCaptcha(text) {
    const colors = ["#e74c3c","#2980b9","#27ae60","#8e44ad","#d35400","#2c3e50"];
    let html = "";

    for (let i = 0; i < text.length; i++) {
        let color = colors[Math.floor(Math.random() * colors.length)];
        let rotate = Math.floor(Math.random() * 20 - 10);

        html += `<span style="color:${color}; display:inline-block; transform:rotate(${rotate}deg);">
                    ${text[i]}
                 </span>`;
    }

    document.getElementById("captchaDisplay").innerHTML = html;
}

document.addEventListener("DOMContentLoaded", function () {
    styleCaptcha($("#serverCaptcha").val());
});

function refreshCaptcha() {
    fetch(window.appRoot+'User/RefreshCaptcha')
        .then(res => res.json())
        .then(data => {
            $("#serverCaptcha").val(data.captcha);
            styleCaptcha(data.captcha);
        });
}


    $("#btnLogin").click(function () {
        var adid = $("#ADID").val().trim();
        var password = $("#password").val().trim();
        var captchaEntered = $("#captchaInput").val().trim();
    var serverCaptcha = $("#serverCaptcha").val();

            if (captchaEntered.toLowerCase() !== serverCaptcha.toLowerCase()) {
        Swal.fire({
            icon: 'error',
            title: 'Invalid Captcha ❌',
            text: 'Captcha mismatch.'
        });

        refreshCaptcha();
        $("#captchaInput").val("");
        return;
    }

        if (!adid || !password) {
            Swal.fire({
                icon: 'warning',
                title: 'Missing Information',
                text: 'Please enter both ADID and password.',
                confirmButtonColor: '#3085d6'
            });
            return;
        }

        showLoading(true);

        $.ajax({
            url: '@Url.Action("Login", "User")',
            type: 'POST',
            contentType: 'application/json',
            data: JSON.stringify({ ADID: adid, Password: password , Captcha: captchaEntered}),
            success: function (response) {
                showLoading(false);

                if (response.success) {
                    Swal.fire({
                        title: '🎉 Welcome Back!',
                       html: `<img src="${window.appRoot}images/img9.jpg" width="150">`,
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
                        window.location.href = window.appRoot + 'TPR/Homepage';
                    }, 1800);
                } else {

                    Swal.fire({
                        title: 'Login Failed!',
                        html: `<img src="${window.appRoot}images/img14.gif" width="150">`,
                        showConfirmButton: false,
                        timer: 3000,
                        background: '#f4f6f9',
                        backdrop: `
                            rgb(121 0 0 / 40%)                       
                            left top
                            no-repeat
                        `
                    });

                      refreshCaptcha();
        $("#captchaInput").val("");
         $("#password").val("");
        return;
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
