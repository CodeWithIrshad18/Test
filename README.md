I have this linq expression 

 var userData = await context.AppEmplMasters
     .Where(x => x.Pno == appLogin.UserId)
     .Select(x => new
     {
         Name = x.Ename,
         Dept = x.DepartmentName,
     })
     .FirstOrDefaultAsync();

 if (userData != null)
 {
     string subject = $"{userData.Name} ({userData.Dept}): Your password has been changed";
     string msg = $"<br/>Your password of Attendance Recording System has been changed to {randomPassword}<br/>" +
                  "<br/>Kindly change the password after login.<br/>" +
                  "Regards,<br/>" +
                  "Tata Steel UISL<br/>";

     await emailService.SendEmailAsync(emailId, "", "", subject, msg);

     ViewBag.Msg = "Mail sent to: " + emailId;
 }

and this is my query 

select Ema_Ename,Ema_Dept_desc from SAPHRDB.dbo.T_EMPL_ALL where ema_perno ='151514'


I want to implement this query in place of linq
