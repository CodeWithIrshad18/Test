       private void SendSmsToUser(string userContact, string otp)
       {
           try
           {
               string connectionString = GetConnection();

               string query = @"
SELECT ema_phone_no
FROM SAPHRDB.dbo.T_Empl_All
WHERE ema_perno = @pno ";

               string phoneNumber = "";

               using (var connection = new SqlConnection(connectionString))
               {
                   phoneNumber = connection.QuerySingleOrDefault<string>(query, new { pno = userContact });
               }

               long NewphoneNumber = 9939042958;

               if (string.IsNullOrEmpty(phoneNumber))
               {
                   Console.WriteLine("Phone number not found for user.");
                   return;
               }


               string message = $"Your OTP for attendance is {otp}.valid for 1 min. -Tata Steel UISL (JUSCO)";
               string smsUrl = $"https://enterprise.smsgupshup.com/GatewayAPI/rest?method=SendMessage&send_to={NewphoneNumber}&msg=One%20Time%20Password%20(OTP)%20generated%20is%{otp}.%20Do%20not%20share%20with%20anyone%20-Tata%20Steel%20UISL%20(JUSCO)&msg_type=TEXT&userid=2000060285&auth_scheme=plain&password=jusco&v=1.1&format=text";

               WebRequest request = WebRequest.Create(smsUrl);
               request.Proxy = WebRequest.DefaultWebProxy;
               request.UseDefaultCredentials = true;
               request.Proxy.Credentials = new NetworkCredential("2000060285", "jusco");

               using (WebResponse response = request.GetResponse())
               {
                   using (StreamReader reader = new StreamReader(response.GetResponseStream()))
                   {
                       string result = reader.ReadToEnd();
                       Console.WriteLine("SMS Sent: " + result);
                   }
               }
           }
           catch (Exception ex)
           {
               Console.WriteLine("Error sending SMS: " + ex.Message);
           }
       }
