using System;
using System.IO;
using System.Net;
using System.Net.Mail;

class Program
{
    static void Main()
    {
        // 1️⃣ Example data — replace with actual values
        string vendorName = "M/s ABC Contractors";
        string vendorCode = "VND123";
        string refNo = "REF2025-001";
        string expiryDate = "05/11/2025";
        string toEmail = "vendor@example.com";

        // 2️⃣ Load the HTML email template (you can store it in a separate .html file)
        string htmlTemplate = @"
<!doctype html>
<html>
<head><meta charset='utf-8' /></head>
<body style='font-family: Arial, Helvetica, sans-serif; color:#222; margin:0; padding:20px; background:#f6f6f6;'>
  <table role='presentation' width='100%' cellpadding='0' cellspacing='0' style='max-width:700px; margin:0 auto; background:#ffffff; border:1px solid #e0e0e0;'>
    <tr>
      <td style='padding:18px 22px;'>
        <h2 style='margin:0 0 8px 0; font-size:18px; color:#0b3861;'>
          Notice of expiry of validity period of labour license / self-declaration
        </h2>
        <p style='margin:0 0 14px 0; color:#444;'>Ref No: <strong>{REF_NO}</strong></p>
        <p style='margin:0 0 18px 0; color:#666;'><strong>Date:</strong> {DATE}</p>
        <hr style='border:none; border-top:1px solid #efefef; margin:12px 0 18px 0;'>

        <p>To<br>
        <strong>M/s {VENDOR_NAME}</strong><br>
        Vendor Code – <strong>{VENDOR_CODE}</strong></p>

        <p style='margin:16px 0 6px 0;'>Dear Sir/Madam,</p>
        <p style='margin:6px 0 12px 0; color:#444;'>
          As per our records, the validity of your <strong>Labour License</strong> or <strong>Self-Declaration</strong> (Ref. No. <strong>{REF_NO}</strong>) is set to expire on <strong>{EXPIRY_DATE}</strong>.
        </p>

        <p style='margin:6px 0 12px 0; color:#444;'>
          You are hereby advised to renew or revalidate the document before the expiry date mentioned above.
        </p>

        <p style='margin:8px 0 12px 0; color:#444;'>
          Alternatively, if no further work is to be executed, please declare “<strong>End of Job</strong>” via the HELPDESK portal.
        </p>

        <p style='margin:18px 0 6px 0; color:#444;'>
          Thanks &amp; Regards,<br>
          <strong>Contractors' Cell</strong><br>
          TATA STEEL UISL
        </p>

        <p style='margin:10px 0 0 0; font-size:12px; color:#888;'>
          Note: This is a system-generated email. Please do not reply.
        </p>
      </td>
    </tr>
  </table>
</body>
</html>";

        // 3️⃣ Replace placeholders dynamically
        string htmlBody = htmlTemplate
            .Replace("{VENDOR_NAME}", vendorName)
            .Replace("{VENDOR_CODE}", vendorCode)
            .Replace("{REF_NO}", refNo)
            .Replace("{EXPIRY_DATE}", expiryDate)
            .Replace("{DATE}", DateTime.Now.ToString("dd/MM/yyyy"));

        // 4️⃣ Prepare subject line
        string subject = $"Notice of expiry of labour license/self-declaration (Ref No {refNo}) for {vendorName}, Vendor Code – ({vendorCode})";

        // 5️⃣ Configure and send the email
        try
        {
            using (var message = new MailMessage())
            {
                message.From = new MailAddress("noreply@yourdomain.com", "Contractors' Cell - TATA STEEL UISL");
                message.To.Add(new MailAddress(toEmail));
                message.Subject = subject;
                message.Body = htmlBody;
                message.IsBodyHtml = true;

                // (Optional) Add a plain text alternative
                string plainText = $"Notice: The validity of your labour license/self-declaration (Ref No {refNo}) expires on {expiryDate}. Please renew immediately.";
                var plainView = AlternateView.CreateAlternateViewFromString(plainText, null, "text/plain");
                var htmlView = AlternateView.CreateAlternateViewFromString(htmlBody, null, "text/html");
                message.AlternateViews.Add(plainView);
                message.AlternateViews.Add(htmlView);

                // SMTP setup
                using (var smtp = new SmtpClient("smtp.yourmailserver.com", 587))
                {
                    smtp.Credentials = new NetworkCredential("smtp_username", "smtp_password");
                    smtp.EnableSsl = true;

                    smtp.Send(message);
                    Console.WriteLine("Email sent successfully!");
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Error sending email: " + ex.Message);
        }
    }
}





<!doctype html>
<html>
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
</head>
<body style="font-family: Arial, Helvetica, sans-serif; color:#222222; margin:0; padding:20px; background:#f6f6f6;">
  <table role="presentation" width="100%" cellpadding="0" cellspacing="0" style="max-width:700px; margin:0 auto; background:#ffffff; border:1px solid #e0e0e0;">
    <tr>
      <td style="padding:18px 22px;">
        <h2 style="margin:0 0 8px 0; font-size:18px; color:#b22222;">
          Final Notice of expiry of validity period of labour license / self-declaration
        </h2>
        <p style="margin:0 0 14px 0; color:#444;">Ref No: <strong>{REF_NO}</strong></p>
        <p style="margin:0 0 18px 0; color:#666;"><strong>Date:</strong> {DATE}</p>

        <hr style="border:none; border-top:1px solid #efefef; margin:12px 0 18px 0;">

        <p style="margin:0 0 8px 0;">To<br>
        <strong>M/s {VENDOR_NAME}</strong><br>
        Vendor Code – <strong>{VENDOR_CODE}</strong></p>

        <p style="margin:16px 0 6px 0;">Dear Sir/Madam,</p>

        <p style="margin:6px 0 12px 0; color:#444;">
          As per our records, the validity of your <strong>Labour License</strong> or the period of acceptance of your <strong>Self-Declaration</strong> (as applicable and referenced under Ref. No. <strong>{REF_NO}</strong>) is ending <strong>today, {EXPIRY_DATE}</strong>.
        </p>

        <p style="margin:8px 0 12px 0; color:#444;">
          You are advised to renew or revalidate the document <strong>immediately</strong>. Alternatively, if no further work is to be executed under the said Labour License or Self-Declaration, you must declare “<strong>End of Job</strong>” through the HELPDESK portal by selecting:
        </p>

        <ul style="color:#444; margin:6px 0 14px 20px;">
          <li><strong>Process Category:</strong> End of Job / Notice of Job Closure</li>
          <li><strong>Complaint Category:</strong> Job not to be executed further</li>
        </ul>

        <p style="margin:6px 0 12px 0; color:#444;">
          Please ensure that a letter declaring “<strong>End of Job</strong>” (verified by the concerned department) is attached during submission.
        </p>

        <p style="margin:6px 0 8px 0; color:#444;"><strong>In case of “End of Job” declaration, you are also required to:</strong></p>
        <ul style="color:#444; margin:6px 0 14px 20px;">
          <li>Arrange Full &amp; Final settlement for all contractor workers affected by the job closure.</li>
          <li>Submit the compliance report through the CLMS F&amp;F Module.</li>
        </ul>

        <p style="margin:6px 0 8px 0; color:#d9534f;"><strong>Important:</strong></p>
        <p style="margin:6px 0 12px 0; color:#444;">
          Despite several reminders, no update has been received regarding the renewal or closure of the self-declaration/labour license (<strong>Ref No: {REF_NO}</strong>). As a result, your payment may be placed on hold, and submission of bills through the DBSTS portal will be restricted starting <strong>tomorrow</strong> (i.e., the day following the expiry date).
        </p>

        <p style="margin:8px 0 12px 0; color:#444;">
          You may check the status of your application at:
          <a href="https://services.tsuisl.co.in/CLMS" style="color:#0b3861; text-decoration:underline;">https://services.tsuisl.co.in/CLMS</a>
        </p>

        <p style="margin:6px 0 6px 0; color:#444;">
          For further assistance, please contact your site HR official or visit the Contractor Cell / Site HR Office between <strong>05:00 PM to 06:00 PM</strong> on any working day. You may also reach us at:
        </p>

        <p style="margin:4px 0 16px 0; color:#444;">
          <strong>0657-6652063</strong>, <strong>0657-6652064</strong>, <strong>0657-6652164</strong>
        </p>

        <p style="margin:18px 0 6px 0; color:#444;">
          Thanks &amp; Regards,<br>
          <strong>Contractors' Cell</strong><br>
          TATA STEEL UISL
        </p>

        <p style="margin:10px 0 0 0; font-size:12px; color:#888;">
          Note: This is a system-generated email. Please do not reply.
        </p>
      </td>
    </tr>
  </table>
</body>
</html>





Subject: Final Notice of expiry of validity period of labour license / self-declaration (Ref No…………..) of engaging less than 10 or more workers to M/s Vendor Name), Vendor Code – (123..)
Date: DD/MM/YYYY

To
M/s (Vendor Name)
Vendor Code – (123..)

Dear Sir/Madam
As per our records, the validity of your Labour License or the period of acceptance of your Self-Declaration (as applicable and referenced under Ref. No. …………) is ending today, [DD/MM/YYYY].
You are advised to renew or revalidate the document immediately. Alternatively, if no further work is to be executed under the said Labour License or Self-Declaration, you must declare “End of Job” through the HELPDESK portal by selecting:
•	Process Category: End of Job / Notice of Job Closure
•	Complaint Category: Job not to be executed further
Please ensure that a letter declaring “End of Job” (verified by the concerned department) is attached during submission.
In case of “End of Job” declaration, you are also required to:
•	Arrange Full & Final settlement for all contractor workers affected by the job closure.
•	Submit the compliance report through the CLMS F&F Module.
Important:
Despite several reminders, no update has been received regarding the renewal or closure of the self-declaration/labour license (Ref No: …………). As a result, your payment may be placed on hold, and submission of bills through the DBSTS portal will be restricted starting tomorrow (i.e., the day following the expiry date).
You may check the status of your application at: https://services.tsuisl.co.in/CLMS
For further assistance, please contact your site HR official or visit the Contractor Cell / Site HR Office between 05:00 PM to 06:00 PM on any working day. You may also reach us at: 0657-6652063, 0657-6652064, 0657-6652164
Thanks & Regards,
Contractors' Cell
TATA STEEL UISL
Note: This is a system-generated email. Please do not reply.
