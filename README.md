public class PreventFloodAttribute : ActionFilterAttribute
{
    private readonly int _seconds;
    public PreventFloodAttribute(int seconds = 3)
    {
        _seconds = seconds;
    }

    public override void OnActionExecuting(ActionExecutingContext context)
    {
        var cache = context.HttpContext.RequestServices.GetService<IMemoryCache>();

        string key = $"Flood_{context.HttpContext.Connection.RemoteIpAddress}_" +
                     $"{context.RouteData.Values["action"]}";

        if (cache.TryGetValue(key, out _))
        {
            context.Result = new ContentResult
            {
                Content = "Multiple submissions detected. Try again.",
                StatusCode = StatusCodes.Status429TooManyRequests
            };
            return;
        }

        cache.Set(key, true, DateTimeOffset.Now.AddSeconds(_seconds));
        base.OnActionExecuting(context);
    }
}




<div class="captcha-box2">
    <div class="captcha-noise"></div>
    <span id="captchaDisplay"></span>

    <button type="button" id="refreshCaptchaBtn" onclick="generateColoredCaptcha()">
        <svg width="18" height="18" viewBox="0 0 24 24">
            <path fill="#333"
                d="M12 6V3L8 7l4 4V8c2.21 0 4 1.79 4 4s-1.79 
                   4-4 4-4-1.79-4-4H6c0 3.31 2.69 6 6 6s6-2.69 
                   6-6-2.69-6-6-6z" />
        </svg>
    </button>
</div>

<input type="text" id="captchaInput" class="inputField" placeholder="Enter Captcha">


.captcha-box2 {
    position: relative;
    display: flex;
    align-items: center;
    gap: 10px;
    background: #f7f7f7;
    padding: 8px 12px;
    border-radius: 8px;
    border: 1px solid #ddd;
    overflow: hidden;
}

#captchaDisplay span {
    font-size: 26px;
    font-weight: 600;
    margin-right: 4px;
    font-family: 'Trebuchet MS', sans-serif;
    letter-spacing: 4px;
    position: relative;
    z-index: 5;
}

.captcha-noise {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 1;
    pointer-events: none;
    background-image:
        linear-gradient(120deg, rgba(0,0,0,0.15) 1px, transparent 1px),
        linear-gradient(60deg, rgba(0,0,0,0.1) 1px, transparent 1px),
        linear-gradient(180deg, rgba(0,0,0,0.12) 1px, transparent 1px);
    background-size: 60px 30px, 45px 25px, 30px 20px;
    opacity: 0.6;
}

let generatedCaptcha = "";

function generateColoredCaptcha() {
    const chars = "ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz23456789";
    const colors = ["#e74c3c","#2980b9","#27ae60","#8e44ad","#d35400","#2c3e50"];

    generatedCaptcha = "";
    let html = "";

    for (let i = 0; i < 6; i++) {
        let ch = chars.charAt(Math.floor(Math.random() * chars.length));
        let color = colors[Math.floor(Math.random() * colors.length)];
        let rotate = Math.floor(Math.random() * 20 - 10); // rotate -10 to +10 degrees

        generatedCaptcha += ch;

        html += `<span style="color:${color}; display:inline-block; transform:rotate(${rotate}deg);">
                    ${ch}
                 </span>`;
    }

    document.getElementById("captchaDisplay").innerHTML = html;
}

document.addEventListener("DOMContentLoaded", generateColoredCaptcha);




private static string GenerateStrongPassword(int length = 12)
{
    const string upper = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
    const string lower = "abcdefghijklmnopqrstuvwxyz";
    const string digits = "0123456789";
    const string special = "@#$%&*!?";

    // Ensure password contains at least one of each category
    string allChars = upper + lower + digits + special;

    Random rnd = new Random();
    var passwordChars = new List<char>
    {
        upper[rnd.Next(upper.Length)],
        lower[rnd.Next(lower.Length)],
        digits[rnd.Next(digits.Length)],
        special[rnd.Next(special.Length)]
    };

    // Fill remaining length with random chars
    for (int i = passwordChars.Count; i < length; i++)
    {
        passwordChars.Add(allChars[rnd.Next(allChars.Length)]);
    }

    // Shuffle characters
    return new string(passwordChars.OrderBy(x => rnd.Next()).ToArray());
}

 var randomPassword = GenerateStrongPassword(12);

var (hashedPassword, salt) = hash_Password.HashPassword(randomPassword);

user.Password = hashedPassword;
user.PasswordSalt = salt;

await context.SaveChangesAsync();
   
    
    
    [HttpPost]
    public async Task<IActionResult> ForgetPassword(AppLogin appLogin)
    {
        try
        {

            if (string.IsNullOrEmpty(appLogin.UserId))
            {
                ViewBag.FailedMsg = "UserId is required";
                return View(appLogin);
            }
            var user = await context.AppLogins
                .FirstOrDefaultAsync(x => x.UserId == appLogin.UserId);

            if (user != null)
            {

                string query2 = @"
SELECT EMA_PYRL_AREA 
FROM SAPHRDB.dbo.T_EMPL_ALL 
WHERE EMA_PERNO = @Pno";



                var parameters1 = new { Pno = appLogin.UserId };

                EmpDTO userData1;
                using (var connection = GetRFIDConnectionString())
                {
                    await connection.OpenAsync();
                    userData1 = await connection.QueryFirstOrDefaultAsync<EmpDTO>(query2, parameters1);
                }


                if (userData1.EMA_PYRL_AREA == "JS" || userData1.EMA_PYRL_AREA == "ZZ")
                {

                    ViewBag.FailedMsg = "Contact with IT team for password reset";
                    return View(appLogin);
                }


                string GenerateRandomString(int length)
                {
                    const string chars = "0123456789";
                    Random random = new Random();
                    return new string(Enumerable.Repeat(chars, length)
                        .Select(s => s[random.Next(s.Length)]).ToArray());
                }

                var randomPassword = "TSUISL" + GenerateRandomString(3) + "ARS" + GenerateRandomString(2);


                var (hashedPassword, salt) = hash_Password.HashPassword(randomPassword);

                user.Password = hashedPassword;
                user.PasswordSalt = salt;

                await context.SaveChangesAsync();

                var emailId = user.Email;

                string query = @"
SELECT EMA_ENAME, EMA_DEPT_DESC 
FROM SAPHRDB.dbo.T_EMPL_ALL 
WHERE EMA_PERNO = @Pno";

                var parameters = new { Pno = appLogin.UserId };

                EmpDTO userData;

                using (var connection = GetRFIDConnectionString())
                {
                    await connection.OpenAsync();
                    userData = await connection.QueryFirstOrDefaultAsync<EmpDTO>(query, parameters);
                }

                if (userData != null)
                {
                    string subject = $"{userData.EMA_ENAME} ({userData.EMA_DEPT_DESC}): Your password has been changed";
                    string msg = $"<br/>Your password of Attendance Recording System has been changed to {randomPassword}<br/>" +
                                 "<br/>Kindly change the password after login.<br/>" +
                                 "Regards,<br/>" +
                                 "Tata Steel UISL<br/>";

                    await emailService.SendEmailAsync(emailId, "", "", subject, msg);
                    ViewBag.Msg = "Mail sent to: " + emailId;
                }
                else
                {
                    Console.WriteLine("Email is null");
                }
            }
            else
            {
                ViewBag.FailedMsg = "UserId Not Found : Please Enter Correct UserId";
                return View(appLogin);
            }
        }
        catch (SmtpException smtpEx)
        {
            smtpEx.Message.ToString();
        }
        catch (Exception ex)
        {
            ex.Message.ToString();
        }

        return View(appLogin);
    }
