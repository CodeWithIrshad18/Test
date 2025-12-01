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
