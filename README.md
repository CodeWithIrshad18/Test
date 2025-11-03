<!-- Save this as a string in your C# code and replace placeholders like {VENDOR_NAME} -->
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
        <h2 style="margin:0 0 8px 0; font-size:18px; color:#0b3861;">Notice of expiry of validity period of labour license / self-declaration</h2>
        <p style="margin:0 0 14px 0; color:#444;">Ref No: <strong>{REF_NO}</strong></p>
        <p style="margin:0 0 18px 0; color:#666;"><strong>Date:</strong> {DATE}</p>

        <hr style="border:none; border-top:1px solid #efefef; margin:12px 0 18px 0;">

        <p style="margin:0 0 8px 0;">To<br>
        <strong>M/s {VENDOR_NAME}</strong><br>
        Vendor Code – <strong>{VENDOR_CODE}</strong></p>

        <p style="margin:16px 0 6px 0;">Dear Sir/Madam,</p>

        <p style="margin:6px 0 12px 0; color:#444;">
          As per our records, the validity of your <strong>Labour License</strong> or the period of acceptance of your <strong>Self-Declaration</strong> (as applicable and referenced under Ref. No. <strong>{REF_NO}</strong>) is set to expire on <strong>{EXPIRY_DATE}</strong>.
        </p>

        <p style="margin:8px 0 12px 0; color:#444;">
          You are hereby advised to renew or revalidate the document before the expiry date mentioned above. Alternatively, if no further work is to be executed under the said Labour License or Self-Declaration, you must declare “<strong>End of Job</strong>” through the HELPDESK portal by selecting:
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
          Failure to renew the document or submit the “End of Job” declaration will result in:
        </p>
        <ul style="color:#444; margin:6px 0 14px 20px;">
          <li>Payment hold, and/or</li>
          <li>Restriction on bill submission through the DBSTS portal, effective from the day following the expiry date.</li>
        </ul>

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


using System.Net.Mail;
using System.Net;

string vendorName = "M/s Vendor Name";
string vendorCode = "123..";
string refNo = "ABC-123";
string dt = DateTime.Now.ToString("dd/MM/yyyy");
string expiryDate = "31/12/2025";

string htmlBody = @"
<!doctype html>
<html>
<head><meta charset='utf-8' /></head>
<body style='font-family: Arial, Helvetica, sans-serif; color:#222; margin:0; padding:20px;'>
  <!-- (Paste the inner HTML from the template above here) -->
  <table role='presentation' width='100%' cellpadding='0' cellspacing='0' style='max-width:700px; margin:0 auto; background:#fff; border:1px solid #e0e0e0;'>
    <tr><td style='padding:18px 22px;'>
      <h2 style='margin:0 0 8px 0; font-size:18px; color:#0b3861;'>Notice of expiry of validity period of labour license / self-declaration</h2>
      <p style='margin:0 0 14px 0; color:#444;'>Ref No: <strong>{REF_NO}</strong></p>
      <p style='margin:0 0 18px 0; color:#666;'><strong>Date:</strong> {DATE}</p>
      <hr style='border:none; border-top:1px solid #efefef; margin:12px 0 18px 0;'>
      <p style='margin:0 0 8px 0;'>To<br><strong>{VENDOR_NAME}</strong><br>Vendor Code – <strong>{VENDOR_CODE}</strong></p>
      <p style='margin:16px 0 6px 0;'>Dear Sir/Madam,</p>
      <p style='margin:6px 0 12px 0; color:#444;'>As per our records, the validity of your <strong>Labour License</strong> or the period of acceptance of your <strong>Self-Declaration</strong> (as applicable and referenced under Ref. No. <strong>{REF_NO}</strong>) is set to expire on <strong>{EXPIRY_DATE}</strong>.</p>
      <p style='margin:8px 0 12px 0; color:#444;'>You are hereby advised to renew or revalidate the document before the expiry date mentioned above. Alternatively, if no further work is to be executed under the said Labour License or Self-Declaration, you must declare “<strong>End of Job</strong>” through the HELPDESK portal by selecting:</p>
      <ul style='color:#444; margin:6px 0 14px 20px;'>
        <li><strong>Process Category:</strong> End of Job / Notice of Job Closure</li>
        <li><strong>Complaint Category:</strong> Job not to be executed further</li>
      </ul>
      <p style='margin:6px 0 12px 0; color:#444;'>Please ensure that a letter declaring “<strong>End of Job</strong>” (verified by the concerned department) is attached during submission.</p>
      <p style='margin:6px 0 8px 0; color:#444;'><strong>In case of “End of Job” declaration, you are also required to:</strong></p>
      <ul style='color:#444; margin:6px 0 14px 20px;'>
        <li>Arrange Full &amp; Final settlement for all contractor workers affected by the job closure.</li>
        <li>Submit the compliance report through the CLMS F&amp;F Module.</li>
      </ul>
      <p style='margin:6px 0 8px 0; color:#d9534f;'><strong>Important:</strong></p>
      <p style='margin:6px 0 12px 0; color:#444;'>Failure to renew the document or submit the “End of Job” declaration will result in:</p>
      <ul style='color:#444; margin:6px 0 14px 20px;'>
        <li>Payment hold, and/or</li>
        <li>Restriction on bill submission through the DBSTS portal, effective from the day following the expiry date.</li>
      </ul>
      <p style='margin:8px 0 12px 0; color:#444;'>You may check the status of your application at: <a href='https://services.tsuisl.co.in/CLMS' style='color:#0b3861; text-decoration:underline;'>https://services.tsuisl.co.in/CLMS</a></p>
      <p style='margin:6px 0 6px 0; color:#444;'>For further assistance, please contact your site HR official or visit the Contractor Cell / Site HR Office between <strong>05:00 PM to 06:00 PM</strong> on any working day. You may also reach us at:</p>
      <p style='margin:4px 0 16px 0; color:#444;'><strong>0657-6652063</strong>, <strong>0657-6652064</strong>, <strong>0657-6652164</strong></p>
      <p style='margin:18px 0 6px 0; color:#444;'>Thanks &amp; Regards,<br><strong>Contractors'' Cell</strong><br>TATA STEEL UISL</p>
      <p style='margin:10px 0 0 0; font-size:12px; color:#888;'>Note: This is a system-generated email. Please do not reply.</p>
    </td></tr>
  </table>
</body>
</html>
";

// replace placeholders
htmlBody = htmlBody.Replace("{VENDOR_NAME}", vendorName)
                   .Replace("{VENDOR_CODE}", vendorCode)
                   .Replace("{REF_NO}", refNo)
                   .Replace("{DATE}", dt)
                   .Replace("{EXPIRY_DATE}", expiryDate);

// subject
string subject = $"Notice of expiry of validity period of labour license / self-declaration (Ref No {refNo}) of engaging less than 10 or more workers to {vendorName}, Vendor Code – ({vendorCode})";

// configure MailMessage
using (var message = new MailMessage())
{
    message.From = new MailAddress("noreply@yourdomain.com", "Contractors' Cell - TATA STEEL UISL");
    message.To.Add(new MailAddress("vendor@example.com")); // replace with recipient
    message.Subject = subject;
    message.Body = htmlBody;
    message.IsBodyHtml = true;

    // optional: add plain-text alt view
    var plainText = "Notice: The validity of your Labour License or Self-Declaration is set to expire on " + expiryDate + ". Please renew or declare End of Job via HELPDESK portal: https://services.tsuisl.co.in/CLMS";
    var plainView = AlternateView.CreateAlternateViewFromString(plainText, null, "text/plain");
    var htmlView = AlternateView.CreateAlternateViewFromString(htmlBody, null, "text/html");
    message.AlternateViews.Add(plainView);
    message.AlternateViews.Add(htmlView);

    // send using SmtpClient (example)
    using (var smtp = new SmtpClient("smtp.yourserver.com", 587))
    {
        smtp.Credentials = new NetworkCredential("smtp_user", "smtp_password");
        smtp.EnableSsl = true;
        smtp.Send(message);
    }
}






SELF DECLARATION/ LABOUR LICENSE SCHEDUALR

1. 1st Alert - Before expiry of 60 days
2. 2nd – Reminder (First) – before expiry of 50 days
3. 3rd – Reminder (Second) - before expiry of 40 days
4. 4th - Reminder (Third) - before expiry of 30 days
5. 5th - Reminder (Fourth - before expiry of 20 days
6. 6th - Reminder (Fifth) - before expiry of 10 days

Subject: Notice of expiry of validity period of labour license / self-declaration (Ref No…………..) of engaging less than 10 or more workers to M/s Vendor Name), Vendor Code – (123..)
Date: DD/MM/YYYY


To
M/s (Vendor Name)
Vendor Code – (123..)

Dear Sir/Madam,
As per our records, the validity of your Labour License or the period of acceptance of your Self-Declaration (as applicable and referenced under Ref. No. …………) is set to expire on [DD/MM/YYYY].
You are hereby advised to renew or revalidate the document before the expiry date mentioned above. Alternatively, if no further work is to be executed under the said Labour License or Self-Declaration, you must declare “End of Job” through the HELPDESK portal by selecting:
• Process Category: End of Job / Notice of Job Closure
• Complaint Category: Job not to be executed further
Please ensure that a letter declaring “End of Job” (verified by the concerned department) is attached during submission.
In case of “End of Job” declaration, you are also required to:
• Arrange Full & Final settlement for all contractor workers affected by the job closure.
• Submit the compliance report through the CLMS F&F Module.
Important:
Failure to renew the document or submit the “End of Job” declaration will result in:
• Payment hold, and/or
• Restriction on bill submission through the DBSTS portal, effective from the day following the expiry date.
You may check the status of your application at: https://services.tsuisl.co.in/CLMS
For further assistance, please contact your site HR official or visit the Contractor Cell / Site HR Office between 05:00 PM to 06:00 PM on any working day. You may also reach us at: 0657-6652063, 0657-6652064, 0657-6652164
Thanks & Regards,
Contractors' Cell
TATA STEEL UISL
Note: This is a system-generated email. Please do not reply.

7. 7th – Reminder (Final) – End of Expiry Day

Subject: Final Notice of expiry of validity period of labour license / self-declaration (Ref No…………..) of engaging less than 10 or more workers to M/s Vendor Name), Vendor Code – (123..)
Date: DD/MM/YYYY

To
M/s (Vendor Name)
Vendor Code – (123..)

Dear Sir/Madam
As per our records, the validity of your Labour License or the period of acceptance of your Self-Declaration (as applicable and referenced under Ref. No. …………) is ending today, [DD/MM/YYYY].
You are advised to renew or revalidate the document immediately. Alternatively, if no further work is to be executed under the said Labour License or Self-Declaration, you must declare “End of Job” through the HELPDESK portal by selecting:
• Process Category: End of Job / Notice of Job Closure
• Complaint Category: Job not to be executed further
Please ensure that a letter declaring “End of Job” (verified by the concerned department) is attached during submission.
In case of “End of Job” declaration, you are also required to:
• Arrange Full & Final settlement for all contractor workers affected by the job closure.
• Submit the compliance report through the CLMS F&F Module.
Important:
Despite several reminders, no update has been received regarding the renewal or closure of the self-declaration/labour license (Ref No: …………). As a result, your payment may be placed on hold, and submission of bills through the DBSTS portal will be restricted starting tomorrow (i.e., the day following the expiry date).
You may check the status of your application at: https://services.tsuisl.co.in/CLMS
For further assistance, please contact your site HR official or visit the Contractor Cell / Site HR Office between 05:00 PM to 06:00 PM on any working day. You may also reach us at: 0657-6652063, 0657-6652064, 0657-6652164
Thanks & Regards,
Contractors' Cell
TATA STEEL UISL
Note: This is a system-generated email. Please do not reply.
