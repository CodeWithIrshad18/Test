   
     [HttpPost]
        public async Task<IActionResult> Login(AppLogin login, string returnUrl = null)
        {

            if (!string.IsNullOrEmpty(login.UserId) && string.IsNullOrEmpty(login.Password))
            {
                ViewBag.FailedMsg = "Login Failed: Password is required";
                return View(login);
            }

            string serverCaptcha = HttpContext.Session.GetString("CaptchaCode");

            if (serverCaptcha == null)
                return Json(new { success = false, message = "Captcha expired" });

            if (login.Captcha == null || login.Captcha.ToLower() != serverCaptcha.ToLower())
                return Json(new { success = false, message = "Invalid Captcha" });


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



                    HttpContext.Session.SetString("Session", userLoginData?.EMA_PERNO ?? "N/A");
                    HttpContext.Session.SetString("UserName", userLoginData?.EMA_ENAME ?? "Guest");


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


                    if (!string.IsNullOrEmpty(returnUrl))
                    {
                        return Redirect(returnUrl);
                    }
                    else
                    {
                       
                        return RedirectToAction("Homepage", "PS");
                    }
                }
                else
                {
                    TempData["Failed"] = "Login Failed: Incorrect Password!";
                    return RedirectToAction("Login", "User");
                }
            }
            else
            {

                TempData["warning"] = "Login Failed: User not found";
                return RedirectToAction("Login", "User");
            }
 
        }


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
            data: JSON.stringify({ ADID: adid, Password: password , Captcha: captchaEntered}),
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
