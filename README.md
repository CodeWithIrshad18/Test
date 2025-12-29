public class OtpVerifyModel
{
    public string UserId { get; set; }
    public string Otp { get; set; }
}
[HttpPost]
public async Task<IActionResult> Login([FromBody] AppLogin login)
{
    string serverCaptcha = HttpContext.Session.GetString("CaptchaCode");

    if (serverCaptcha == null || login.Captcha?.ToLower() != serverCaptcha.ToLower())
        return Json(new { success = false, message = "Invalid Captcha" });

    var user = await context.AppLogins
        .FirstOrDefaultAsync(x => x.UserId == login.UserId);

    if (user == null)
        return Json(new { success = false, message = "User not found" });

    bool isPasswordValid = hash_Password.VerifyPassword(login.Password, user.Password, user.PasswordSalt);

    if (!isPasswordValid)
        return Json(new { success = false, message = "Incorrect password" });

    // ✅ Generate OTP
    string otp = new Random().Next(100000, 999999).ToString();

    HttpContext.Session.SetString("LoginOTP", otp);
    HttpContext.Session.SetString("OTPUser", login.UserId);
    HttpContext.Session.SetString("OTPTimestamp", DateTime.Now.ToString());

    // ✅ Send OTP SMS
    SendSmsToUser(login.UserId, otp);

    return Json(new { success = true, otpRequired = true });
}

[HttpPost]
public IActionResult VerifyOtp([FromBody] OtpVerifyModel model)
{
    var sessionOtp = HttpContext.Session.GetString("LoginOTP");
    var sessionUser = HttpContext.Session.GetString("OTPUser");

    if (sessionOtp == null || sessionUser != model.UserId)
        return Json(new { success = false, message = "OTP expired" });

    if (sessionOtp != model.Otp)
        return Json(new { success = false, message = "Invalid OTP" });

    // ✅ Clear OTP
    HttpContext.Session.Remove("LoginOTP");

    // ✅ Set Cookies (FINAL LOGIN)
    var cookieOptions = new CookieOptions
    {
        Expires = DateTimeOffset.Now.AddYears(1),
        HttpOnly = true,
        Secure = true,
        SameSite = SameSiteMode.Strict
    };

    Response.Cookies.Append("UserSession", model.UserId, cookieOptions);

    return Json(new { success = true });
}

<div class="modal fade" id="otpModal" tabindex="-1">
  <div class="modal-dialog modal-dialog-centered">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title">🔐 Enter OTP</h5>
      </div>
      <div class="modal-body">
        <input type="text" id="otpInput" class="form-control" placeholder="Enter 6-digit OTP">
      </div>
      <div class="modal-footer">
        <button class="btn btn-primary" id="verifyOtpBtn">Verify OTP</button>
      </div>
    </div>
  </div>
</div>

success: function (response) {
    showLoading(false);

    if (response.otpRequired) {
        $("#otpModal").modal("show");
        return;
    }
}


$("#verifyOtpBtn").click(function () {

    let otp = $("#otpInput").val().trim();
    let userId = $("#UserId").val();

    if (!otp) {
        Swal.fire("Enter OTP");
        return;
    }

    $.ajax({
        url: window.appRoot + "User/VerifyOtp",
        type: "POST",
        contentType: "application/json",
        data: JSON.stringify({
            UserId: userId,
            Otp: otp
        }),
        success: function (res) {
            if (res.success) {
                Swal.fire("Login Successful 🎉");

                setTimeout(() => {
                    window.location.href = window.appRoot + "Face/GeoFencing";
                }, 1200);
            } else {
                Swal.fire(res.message);
            }
        }
    });
});




<div class="container">
                         <div class="col-lg-12 d-flex justify-content-center order-1 order-lg-0">
                      <div class="form-bg">
<form action="Login" class="form_main">

  
    <p class="heading text-center">  <img src="~/AppImages/logo6.png" width="75%" height="30%;"></p>
    <div class="inputContainer">
        <svg class="inputIcon" width="16" height="16" fill="#2e2e2e" viewBox="0 0 16 16">
        <path d="M13.106 7.222c0-2.967-2.249-5.032-5.482-5.032-3.35 0-5.646 2.318-5.646 5.702 0 3.493 2.235 5.708 5.762 5.708.862 0 1.689-.123 2.304-.335v-.862c-.43.199-1.354.328-2.29.328-2.926 0-4.813-1.88-4.813-4.798 0-2.844 1.921-4.881 4.594-4.881 2.735 0 4.608 1.688 4.608 4.156 0 1.682-.554 2.769-1.416 2.769-.492 0-.772-.28-.772-.76V5.206H8.923v.834h-.11c-.266-.595-.881-.964-1.6-.964-1.4 0-2.378 1.162-2.378 2.823 0 1.737.957 2.906 2.379 2.906.8 0 1.415-.39 1.709-1.087h.11c.081.67.703 1.148 1.503 1.148 1.572 0 2.57-1.415 2.57-3.643zm-7.177.704c0-1.197.54-1.907 1.456-1.907.93 0 1.524.738 1.524 1.907S8.308 9.84 7.371 9.84c-.895 0-1.442-.725-1.442-1.914z"></path>
        </svg>
    <input asp-for="UserId" type="number" oninput="javascript: if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);" maxlength="6" class="inputField" id="UserId" placeholder="UserID" autocomplete="off">
    </div>
    
<div class="inputContainer">
    <svg class="inputIcon" width="16" height="16" fill="#2e2e2e" viewBox="0 0 16 16">
    <path d="M8 1a2 2 0 0 1 2 2v4H6V3a2 2 0 0 1 2-2zm3 6V3a3 3 0 0 0-6 0v4a2 2 0 0 0-2 2v5a2 2 0 0 0 2 2h6a2 2 0 0 0 2-2V9a2 2 0 0 0-2-2z"></path>
    </svg>
    <input asp-for="Password" type="password" type="password" class="inputField" id="password" placeholder="Password" autocomplete="off">
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
<div class="remember-forgot mt-2">
          
            <a href="~/User/ForgetPassword" style="font-size:11px;">Forgot Password?</a>
        </div>
</form>
</div>


Js :

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



$("#btnLogin").click(function (e) {
    e.preventDefault();

    var UserId = $("#UserId").val().trim();
    var password = $("#password").val().trim();
    var captchaEntered = $("#captchaInput").val().trim();
    var serverCaptcha = $("#serverCaptcha").val();

    if (!UserId || !password) {
            Swal.fire({
                icon: 'warning',
                title: 'Missing Information',
                text: 'Please enter both UserId and password.',
                confirmButtonColor: '#3085d6'
            });
            return;
        }

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


    showLoading(true);

    $.ajax({
        url: '@Url.Action("Login", "User")',
        type: 'POST',
        contentType: 'application/json',
        data: JSON.stringify({
    UserId: UserId,
    Password: password,
    Captcha: captchaEntered
}),

        success: function (response) {
            showLoading(false);

            if (response.success) {
                Swal.fire({
                    title: '🎉 Welcome Back!',
                    html: `<img src="${window.appRoot}AppImages/img9.jpg" width="150">`,
                    showConfirmButton: false,
                    timer: 2500,
                    background: '#f4f6f9',
                    backdrop: `
                            rgba(0,0,123,0.4)
                            url("/images/party.gif")
                            left top
                            no-repeat
                        `
                });

                setTimeout(function () {
                    window.location.href = window.appRoot + 'Face/GeoFencing';
                }, 1800);
            } else {
               Swal.fire({
                        title: 'Login Failed!',
                         html: `<img src="${window.appRoot}AppImages/img14.gif" width="150">`,
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
                text: 'Unable to contact server.'
            });
        }
    });
});

</script>






 [HttpPost]
 public async Task<IActionResult> Login([FromBody] AppLogin login)
 {
     string serverCaptcha = HttpContext.Session.GetString("CaptchaCode");

     if (serverCaptcha == null)
         return Json(new { success = false, message = "Captcha expired" });

     if (login.Captcha == null || login.Captcha.ToLower() != serverCaptcha.ToLower())
         return Json(new { success = false, message = "Invalid Captcha" });


     if (login == null || string.IsNullOrEmpty(login.UserId) || string.IsNullOrEmpty(login.Password))
         return Json(new { success = false, message = "Invalid login data" });

     var user = await context.AppLogins
         .FirstOrDefaultAsync(x => x.UserId == login.UserId);

     if (user == null)
         return Json(new { success = false, message = "User not found" });

     bool isPasswordValid = hash_Password.VerifyPassword(login.Password, user.Password, user.PasswordSalt);

     if (!isPasswordValid)
         return Json(new { success = false, message = "Incorrect password" });

     string query = @"SELECT EMA_PERNO, EMA_ENAME FROM SAPHRDB.dbo.T_Empl_All WHERE EMA_PERNO = @Pno";
     var parameters = new { Pno = login.UserId };

     EmpDTO userLoginData;
     using (var connection = GetRFIDConnectionString())
     {
         await connection.OpenAsync();
         userLoginData = await connection.QueryFirstOrDefaultAsync<EmpDTO>(query, parameters);
     }

     string userName = userLoginData?.EMA_ENAME ?? "Guest";
     string userPno = userLoginData?.EMA_PERNO ?? "N/A";

     // SET COOKIES
     var cookieOptions = new CookieOptions
     {
         Expires = DateTimeOffset.Now.AddYears(1),
         HttpOnly = true,
         Secure = true,
         IsEssential = true,
         SameSite = SameSiteMode.Strict
     };

     Response.Cookies.Append("UserSession", login.UserId, cookieOptions);         
     Response.Cookies.Append("Session", userPno, cookieOptions);
     Response.Cookies.Append("UserName", userName, cookieOptions);

     return Json(new { success = true });
 }


 private void SendSmsToUser(string userContact, string otp)
 {
     try
     {
         string connectionString = GetRFIDConnectionString();

         string query = @"
 SELECT Phone
 FROM UserLoginDB.dbo.App_EmployeeMaster
 WHERE Pno = @pno ";

         string phoneNumber = "";

         using (var connection = new SqlConnection(connectionString))
         {
             phoneNumber = connection.QuerySingleOrDefault<string>(query, new { pno = userContact });
         }

         //long NewphoneNumber = 6201848723;

         if (string.IsNullOrEmpty(phoneNumber))
         {
             Console.WriteLine("Phone number not found for user.");
             return;
         }


         string message = $"Your OTP for attendance is {otp}.valid for 1 min. -Tata Steel UISL (JUSCO)";
         string smsUrl = $"https://enterprise.smsgupshup.com/GatewayAPI/rest?method=SendMessage&send_to={phoneNumber}&msg={Uri.EscapeDataString(message)}&msg_type=TEXT&userid=2000060285&auth_scheme=plain&password=jusco&v=1.1&format=text";

         WebRequest request = WebRequest.Create(smsUrl);
         request.Proxy = WebRequest.DefaultWebProxy;
         request.UseDefaultCredentials = true;
         request.Proxy.Credentials = new NetworkCredential("juscosms", "oR#543AD");

         using (WebResponse response = request.GetResponse())
         {
             using (StreamReader reader = new StreamReader(response.GetResponseStream()))
             {
                 string result = reader.ReadToEnd();
                 Console.WriteLine("SMS Sent: " + result);
             }
         }
     }
     catch (Exception ex)
     {
         Console.WriteLine("Error sending SMS: " + ex.Message);
     }
 }
