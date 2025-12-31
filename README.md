private void SendSmsToUser(string userContact, string otp)
{
    try
    {
        string connectionString = GetConnection();

        string query = @"
    SELECT ema_phone_no
    FROM SAPHRDB.dbo.T_Empl_All
    WHERE ema_perno = @pno";

        long phoneNumber;

        using (var connection = new SqlConnection(connectionString))
        {
            phoneNumber = connection.QuerySingleOrDefault<long>(query, new { pno = userContact });
        }

   
        //long NewphoneNumber = 6201848723;

        if (phoneNumber==null)
        {
            Console.WriteLine("Phone number not found for user.");
            return;
        }


        string message =
            $"One Time Password (OTP) generated is {otp}. " +
            $"Valid for 5 minute. Do not share with anyone. " +
            $"- Tata Steel UISL (JUSCO)";


        string smsUrl =
            "https://enterprise.smsgupshup.com/GatewayAPI/rest?method=SendMessage" +
            $"&send_to={phoneNumber}" +
            $"&msg={Uri.EscapeDataString(message)}" +
            "&msg_type=TEXT" +
            "&userid=2000060285" +
            "&auth_scheme=plain" +
            "&password=jusco" +
            "&v=1.1" +
            "&format=text";

        WebRequest request = WebRequest.Create(smsUrl);
        request.Proxy = WebRequest.DefaultWebProxy;
        request.UseDefaultCredentials = true;
        request.Proxy.Credentials = new NetworkCredential("2000060285", "jusco");

        using (WebResponse response = request.GetResponse())
        using (StreamReader reader = new StreamReader(response.GetResponseStream()))
        {
            string result = reader.ReadToEnd();
            Console.WriteLine("SMS Sent Successfully: " + result);
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error sending SMS: " + ex.Message);
    }
}
