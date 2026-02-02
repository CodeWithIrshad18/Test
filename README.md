string subject = "TPR Login OTP";

string body = $@"
<!DOCTYPE html>
<html>
<head>
    <meta charset='UTF-8'>
    <meta name='viewport' content='width=device-width, initial-scale=1.0'>
</head>
<body style='margin:0;padding:0;background-color:#f4f6f8;font-family:Arial,Helvetica,sans-serif;'>

<table width='100%' cellpadding='0' cellspacing='0' style='background-color:#f4f6f8;padding:20px;'>
    <tr>
        <td align='center'>
            <table width='100%' cellpadding='0' cellspacing='0' 
                   style='max-width:420px;background:#ffffff;border-radius:8px;
                          box-shadow:0 4px 12px rgba(0,0,0,0.08);padding:25px;'>

                <!-- Header -->
                <tr>
                    <td align='center' style='padding-bottom:15px;'>
                        <h2 style='margin:0;color:#1f2937;'>TPR Login Verification</h2>
                    </td>
                </tr>

                <!-- Greeting -->
                <tr>
                    <td style='color:#374151;font-size:14px;padding-bottom:10px;'>
                        Hello <strong>{userLoginData.EMA_ENAME}</strong>,
                    </td>
                </tr>

                <!-- Message -->
                <tr>
                    <td style='color:#374151;font-size:14px;padding-bottom:20px;'>
                        Use the following One-Time Password (OTP) to log in to TPR.
                    </td>
                </tr>

                <!-- OTP Box -->
                <tr>
                    <td align='center' style='padding-bottom:20px;'>
                        <div style='font-size:28px;
                                    letter-spacing:6px;
                                    font-weight:bold;
                                    color:#2563eb;
                                    background:#eff6ff;
                                    padding:12px 20px;
                                    border-radius:6px;
                                    display:inline-block;'>
                            {otp}
                        </div>
                    </td>
                </tr>

                <!-- Validity -->
                <tr>
                    <td align='center' style='font-size:13px;color:#6b7280;padding-bottom:15px;'>
                        Valid for <strong>5 minutes</strong>
                    </td>
                </tr>

                <!-- Warning -->
                <tr>
                    <td align='center' style='font-size:12px;color:#9ca3af;'>
                        ⚠️ Do not share this OTP with anyone.
                    </td>
                </tr>

            </table>

            <!-- Footer -->
            <table width='100%' style='max-width:420px;margin-top:10px;'>
                <tr>
                    <td align='center' style='font-size:11px;color:#9ca3af;'>
                        © {DateTime.Now.Year} TSUISL | Automated Email – Please do not reply
                    </td>
                </tr>
            </table>

        </td>
    </tr>
</table>

</body>
</html>";


 
 
 
 string subject = "TPR Login OTP";
 string body = $"<h3>Hello {userLoginData.EMA_ENAME}, </h3><p>Your OTP for TPR is <b>{otp}</b></p><p>Valid for 5 minutes. Do not share with anyone.</p>";
