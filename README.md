<div class="inputContainer captcha-box">
   <div class="captcha-box2">
    <span id="captchaDisplay"></span>
</div>


<button type="button" id="refreshCaptchaBtn" onclick="generateColoredCaptcha()">
        <svg width="25" height="25" viewBox="0 0 24 24">
            <path fill="#333"
                d="M12 6V3L8 7l4 4V8c2.21 0 4 1.79 4 4s-1.79 
                   4-4 4-4-1.79-4-4H6c0 3.31 2.69 6 6 6s6-2.69 
                   6-6-2.69-6-6-6z" />
        </svg>
</button>


    <input type="text" id="captchaInput" class="inputField" placeholder="Enter Captcha" style="padding-left:8px;font-size:11px;">
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

function generateColoredCaptcha() {
    const chars = "ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz23456789";
    const colors = ["#e74c3c","#2980b9","#27ae60","#8e44ad","#d35400","#2c3e50"];

    generatedCaptcha = "";
    let html = "";

    for (let i = 0; i < 6; i++) {
        let ch = chars.charAt(Math.floor(Math.random() * chars.length));
        let color = colors[Math.floor(Math.random() * colors.length)];

        generatedCaptcha += ch;
        html += `<span style="color:${color}">${ch}</span>`;
    }

    document.getElementById("captchaDisplay").innerHTML = html;
}

document.addEventListener("DOMContentLoaded", generateColoredCaptcha);



   $("#btnLogin").click(function (e) {
    e.preventDefault();

    var adid = $("#ADID").val().trim();
    var password = $("#password").val().trim();
    var captchaEntered = $("#captchaInput").val().trim();

    if (!adid || !password) {
        Swal.fire({
            icon: 'warning',
            title: 'Missing Information',
            text: 'Please enter both UserId and password.'
        });
        return;
    }

    if (captchaEntered !== generatedCaptcha) {
        Swal.fire({
            icon: 'error',
            title: 'Invalid Captcha ❌',
            text: 'Captcha mismatch'
        });
        generateColoredCaptcha();
        $("#captchaInput").val("");
        return;
    }

    showLoading(true);

    $.ajax({
        url: '@Url.Action("Login", "User")',
        type: 'POST',
        contentType: 'application/json',
        data: JSON.stringify({
            UserId: adid,     
            Password: password 
        }),
        success: function (response) {
            showLoading(false);

            if (response.success) {
                Swal.fire({
                    title: '🎉 Welcome Back!',
                    html: '<img src="/TSUISLARS/AppImages/img9.jpg" width="150">',
                    showConfirmButton: false,
                    timer: 2500
                });

                setTimeout(function () {
                    window.location.href = "/TSUISLARS/Face/GeoFencing";
                }, 1800);
            } else {
               Swal.fire({
                        title: 'Login Failed!',
                        html: '<img src="/TSUISLARS/AppImages/img14.gif" width="150">',
                        showConfirmButton: false,
                        timer: 3000,
                        background: '#f4f6f9',
                        backdrop: `
                            rgb(121 0 0 / 40%)                       
                            left top
                            no-repeat
                        `
                    });
                     generateColoredCaptcha();
        $("#captchaInput").val("");
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


        public IActionResult Login()
        {
            return View();
        }

        [HttpPost]
        public async Task<IActionResult> Login([FromBody] AppLogin login)
        {
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


please make my Captcha in Server side with all functionality with refresh button and all
