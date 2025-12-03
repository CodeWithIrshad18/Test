public IActionResult Login()
{
    string captcha = GenerateCaptchaText();
    HttpContext.Session.SetString("CaptchaCode", captcha);

    ViewBag.Captcha = captcha;
    return View();
}

private string GenerateCaptchaText()
{
    const string chars = "ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz23456789";
    Random rnd = new Random();
    return new string(Enumerable.Repeat(chars, 6)
        .Select(s => s[rnd.Next(s.Length)]).ToArray());
}
public IActionResult RefreshCaptcha()
{
    string captcha = GenerateCaptchaText();
    HttpContext.Session.SetString("CaptchaCode", captcha);

    return Json(new { captcha });
}

<input type="hidden" id="serverCaptcha" value="@ViewBag.Captcha" />

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
<button type="button" id="refreshCaptchaBtn" onclick="refreshCaptcha()">

$("#btnLogin").click(function (e) {
    e.preventDefault();

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

    // After captcha matches → Send login AJAX exactly as before

[HttpPost]
public async Task<IActionResult> Login([FromBody] AppLogin login)
{
    string serverCaptcha = HttpContext.Session.GetString("CaptchaCode");

    if (serverCaptcha == null)
        return Json(new { success = false, message = "Captcha expired" });

    if (login.Captcha == null || login.Captcha.ToLower() != serverCaptcha.ToLower())
        return Json(new { success = false, message = "Invalid Captcha" });

data: JSON.stringify({
    UserId: adid,
    Password: password,
    Captcha: captchaEntered
}),

public string Captcha { get; set; }



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
