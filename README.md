 void SendMail(string Email, string body, string subject, string pdfPath)
 {

     using (var mail = new MailMessage())
     {
         mail.From = new MailAddress("automatic_mail@tatasteel.com");
         mail.To.Add(Email);
         mail.Body = body;
         mail.Subject = subject;
         mail.IsBodyHtml = true;

         // Attach PDF
         Attachment pdf = new Attachment(pdfPath);
         mail.Attachments.Add(pdf);

         using (var smtp = new SmtpClient("144.0.11.253", 25))
         {
             smtp.Timeout = 20000;
             smtp.Send(mail);
         }
     }



 }


        private string EmailBody(string strLocation, string fromdt, string Todt, string perno, string strHotel, string Email, string subject, string strEname,string receiptNo)
        {
           


            string body = $@" <body style='margin:0; padding:0; background:#dce6f2; font-family:Arial, sans-serif;'>

<table width='100%' cellpadding='0' cellspacing='0' style='background:#dce6f2; padding:20px 0;'>
<tr>
<td align='center'>

    <table width='700' cellpadding='0' cellspacing='0' style='background:#ffffff; border:1px solid #b8c6d0;'>

        <tr>
            <td style='background:#e6eef6; padding:15px 20px; font-size:18px; font-weight:bold; color:#000; border-bottom:1px solid #c0ccd6;'>
                Holiday Home Booking Confirmed ({receiptNo})
            </td>
        </tr>

        <tr>
            <td style='padding:15px 20px; background:#f5f9fd; border-bottom:1px solid #cfd8e2;'>
                <table cellpadding='0' cellspacing='0'>
                    <tr>
                        <td style='vertical-align:middle;'>
                            <img src='https://cdn-icons-png.flaticon.com/512/2088/2088617.png' width='20' height='20' />
                        </td>
                        <td style='padding-left:10px; font-size:14px; color:#000;'>
                            Created &nbsp;&nbsp; <strong>{perno}</strong>
                        </td>
                    </tr>
                </table>
            </td>
        </tr>

        <tr>
            <td style='padding:25px 20px; background:#e6eef6; font-size:15px; color:#000;'>

                <p>Dear <strong>{strEname} ({perno})</strong>,</p>

                <p>Your application for Holiday Home booking at <strong>{strHotel}</strong>, {strLocation} 
                from <strong>{fromdt}</strong> to <strong>{Todt}</strong> is confirmed.</p>

                <p>Please carry a printout of the booking permit along with you during your visit.</p>

                <p>Kindly comply with the Terms &amp; Conditions given in the permit during your stay.</p>

                <p>Have a pleasant stay ahead!!</p>

                <p>With warm regards,<br/><br/>

                Area Manager,<br/><br/>
                Employment Bureau</p>

            </td>
        </tr>

        <tr>
            <td style='padding:15px 20px; background:#ffffff; border-top:1px solid #cfd8e2;'>
                <a href='' style='font-size:15px; color:#003399; text-decoration:underline;'>
                    Holiday Home Permit
                </a>
            </td>
        </tr>

    </table>

</td>
</tr>
</table>

</body>";
            SendMail(Email, body, subject);
            return body;
        }


error : There is no argument given that corresponds to the required parameter 'pdfPath' of 'HDH_BOOKING_CONFIRMATION.SendMail(string, string, string, string)'
