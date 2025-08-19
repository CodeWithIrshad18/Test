if (userData != null)
{
    string subject = $"{userData.EMA_ENAME} ({userData.EMA_DEPT_DESC}): Your password has been changed";
    string msg = $"<br/>Your password of Attendance Recording System has been changed to {randomPassword}<br/>" +
                 "<br/>Kindly change the password after login.<br/>" +
                 "Regards,<br/>" +
                 "Tata Steel UISL<br/>";

    await emailService.SendEmailAsync(emailId, "", "", subject, msg);

    // Store success message in TempData
    TempData["SuccessMsg"] = $"Password sent successfully to {emailId}";
}
@section Scripts {
    <script>
        document.addEventListener("DOMContentLoaded", function () {
            @if (TempData["SuccessMsg"] != null)
            {
                <text>
                Swal.fire({
                    icon: 'success',
                    title: 'Success',
                    text: '@TempData["SuccessMsg"]',
                    confirmButtonColor: '#3085d6',
                    confirmButtonText: 'OK'
                });
                </text>
            }

            @if (ViewBag.FailedMsg != null)
            {
                <text>
                Swal.fire({
                    icon: 'error',
                    title: 'Failed',
                    text: '@ViewBag.FailedMsg',
                    confirmButtonColor: '#d33',
                    confirmButtonText: 'Retry'
                });
                </text>
            }
        });
    </script>
}



this is my controller code for ForgetPassword    
 [HttpPost]
    public async Task<IActionResult> ForgetPasswordNOPR(AppLogin appLogin)
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

                var emailId = appLogin.Email;

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
        catch (Exception ex)
        {
            ex.Message.ToString();
        }

        return RedirectToAction("ForgetPasswordNOPR","User");
    }

and this is my form 
<form asp-action="ForgetPasswordNOPR" asp-controller="User" method="post">

    <div class="d-flex justify-content-center ">
        @if (ViewBag.FailedMsg != null)
        {
            <div class="alert alert-danger failedMsg text-center">
                @ViewBag.FailedMsg
            </div>
        }
    </div>

    <div class="wrapper login">
        <div class="d-flex justify-content-center">
            <i class="bx bx-lock" style="font-size:45px;color:black"></i>
        </div>
       
        <div class="input-box2">
            <span class="icon"><i class='bx bx-user'></i></span>
            <input asp-for="UserId" type="number" oninput="javascript: if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);" maxlength="6" autocomplete="off" required>
            <label>P.No.</label>
        </div>
        <div class="input-box2">
            <span class="icon"><i class='bx bx-user'></i></span>
            <input asp-for="Email" required>
            <label>Email to sent password</label>
        </div>
        <span id="Email" style="color:red;font-size:12px;" class="">@ViewBag.Msg</span>
       
        <div class="text-center mt-4">
            <button type="submit" class="btn btn-dark col-sm-4">SEND</button>
        </div>


    </div>
</form>

i want to show success message with that email , like in swal fire or something , Email Sent to ****
