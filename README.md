<form asp-action="Login" class="form_main">

  

    <p class="heading">Login</p>
    <div class="inputContainer">
        <svg class="inputIcon" xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="#2e2e2e" viewBox="0 0 16 16">
        <path d="M13.106 7.222c0-2.967-2.249-5.032-5.482-5.032-3.35 0-5.646 2.318-5.646 5.702 0 3.493 2.235 5.708 5.762 5.708.862 0 1.689-.123 2.304-.335v-.862c-.43.199-1.354.328-2.29.328-2.926 0-4.813-1.88-4.813-4.798 0-2.844 1.921-4.881 4.594-4.881 2.735 0 4.608 1.688 4.608 4.156 0 1.682-.554 2.769-1.416 2.769-.492 0-.772-.28-.772-.76V5.206H8.923v.834h-.11c-.266-.595-.881-.964-1.6-.964-1.4 0-2.378 1.162-2.378 2.823 0 1.737.957 2.906 2.379 2.906.8 0 1.415-.39 1.709-1.087h.11c.081.67.703 1.148 1.503 1.148 1.572 0 2.57-1.415 2.57-3.643zm-7.177.704c0-1.197.54-1.907 1.456-1.907.93 0 1.524.738 1.524 1.907S8.308 9.84 7.371 9.84c-.895 0-1.442-.725-1.442-1.914z"></path>
        </svg>
    <input asp-for="UserId" class="inputField" id="ADID"type="number" placeholder="UserId" oninput="javascript: if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);" maxlength="6" autocomplete="off">
    </div>
    
<div class="inputContainer">
    <svg class="inputIcon" xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="#2e2e2e" viewBox="0 0 16 16">
    <path d="M8 1a2 2 0 0 1 2 2v4H6V3a2 2 0 0 1 2-2zm3 6V3a3 3 0 0 0-6 0v4a2 2 0 0 0-2 2v5a2 2 0 0 0 2 2h6a2 2 0 0 0 2-2V9a2 2 0 0 0-2-2z"></path>
    </svg>
    <input asp-for="Password" type="password" class="inputField" id="password" placeholder="Password" autocomplete="off">
</div>

 <input type="hidden" id="serverCaptcha" value="@ViewBag.Captcha" />
    
<div class="inputContainer captcha-box">
   <div class="captcha-box2">
    <span id="captchaDisplay"></span>
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
           
<button type="submit" id="btnLogin">Submit</button>

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
    fetch('/User/RefreshCaptcha')
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
            data: JSON.stringify({ UserId: adid, Password: password , Captcha: captchaEntered}),
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
                        window.location.href = '/PS/Homepage';
                    }, 1800);
                } else {

                    Swal.fire({
                        title: 'Login Failed!',
                        html: '<img src="/images/img14.gif" width="150">',
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


    [HttpPost]
    public async Task<IActionResult> Login([FromBody] AppLogin login, string returnUrl = null)
    {
     
        if (string.IsNullOrEmpty(login.UserId) || string.IsNullOrEmpty(login.Password))
        {
            return Json(new { success = false, message = "ADID and Password are required" });
        }

  
        string serverCaptcha = HttpContext.Session.GetString("CaptchaCode");

        if (string.IsNullOrEmpty(serverCaptcha))
        {
            return Json(new { success = false, message = "Captcha expired" });
        }

        if (string.IsNullOrEmpty(login.Captcha) ||
            !login.Captcha.Equals(serverCaptcha, StringComparison.OrdinalIgnoreCase))
        {
            return Json(new { success = false, message = "Invalid Captcha" });
        }

    
        var user = await context.AppLogins
            .FirstOrDefaultAsync(x => x.UserId == login.UserId);

        if (user == null)
        {
            return Json(new { success = false, message = "User not found" });
        }

        bool isPasswordValid = hash_Password.VerifyPassword(
            login.Password,
            user.Password,
            user.PasswordSalt
        );

        if (!isPasswordValid)
        {
            return Json(new { success = false, message = "Incorrect password" });
        }

    
        string query = @"
    SELECT EMA_PERNO, EMA_ENAME
    FROM SAPHRDB.dbo.T_Empl_All
    WHERE EMA_PERNO = @Pno";

        EmpDTO userLoginData;

        using (var connection = GetRFIDConnectionString())
        {
            await connection.OpenAsync();
            userLoginData = await connection.QueryFirstOrDefaultAsync<EmpDTO>(
                query,
                new { Pno = login.UserId }
            );
        }

        string userName = userLoginData?.EMA_ENAME ?? "Guest";
        string userPno = userLoginData?.EMA_PERNO ?? login.UserId;

        HttpContext.Session.SetString("Session", userPno);
        HttpContext.Session.SetString("UserName", userName);

        var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, userName),
    new Claim("UserId", userPno)
};

        var identity = new ClaimsIdentity(
            claims,
            CookieAuthenticationDefaults.AuthenticationScheme
        );

        await HttpContext.SignInAsync(
            CookieAuthenticationDefaults.AuthenticationScheme,
            new ClaimsPrincipal(identity),
            new AuthenticationProperties { IsPersistent = true }
        );

        return Json(new
        {
            success = true,
            message = "Login successful"
        });
    }

getting this error after successful login

This page isn’t working
If the problem continues, contact the site owner.
HTTP ERROR 415
