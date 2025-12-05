protected void Page_Load(object sender, EventArgs e)
{
    Response.Write("<b>DEBUG MODE ENABLED</b><br/><br/>");

    try
    {
        // ===== HARDCODED TOKEN ALWAYS USED =====
        string hardcodedToken = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9....";

        if (!string.IsNullOrEmpty(hardcodedToken))
        {
            Response.Write("<span style='color:green'>Using HARD-CODED TOKEN</span><br/><br/>");
            DecodeAndSetSession(hardcodedToken);
            return;
        }

        // ===== COOKIE CODE NOT USED IN DEBUG =====
        HttpCookie authCookie = Request.Cookies["accessToken"];

        if (authCookie == null)
        {
            Response.Write("<span style='color:red'>Cookie 'accessToken' not found!</span>");
            return;
        }

        if (string.IsNullOrEmpty(authCookie.Value))
        {
            Response.Write("<span style='color:red'>Cookie value is EMPTY!</span>");
            return;
        }

        DecodeAndSetSession(authCookie.Value);
    }
    catch (Exception ex)
    {
        Response.Write("<span style='color:red'><b>Exception:</b> " + ex.Message + "</span><br/>");
    }
}

public static class JwtSessionHelper
{
    public static void RestoreSessionFromJWT()
    {
        try
        {
            // DEBUG MODE → ALWAYS USE HARDCODED TOKEN
            string jwtToken = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9....";

            var handler = new JwtSecurityTokenHandler();
            var token = handler.ReadJwtToken(jwtToken);

            HttpContext.Current.Session["UserName"] =
                token.Claims.FirstOrDefault(c => c.Type.Contains("/claims/name"))?.Value;

            HttpContext.Current.Session["UserId"] =
                token.Claims.FirstOrDefault(c => c.Type.Contains("/claims/nameidentifier"))?.Value;

            HttpContext.Current.Session["UserEmail"] =
                token.Claims.FirstOrDefault(c => c.Type.Contains("/claims/emailaddress"))?.Value;
        }
        catch { }
    }
}

    
    
    
    public partial class _Default : Classes.basePage 
    {
        string hardcodedToken = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9uYW1lIjoiVEFQQU4gQ0hBS1JBQk9SVFkiLCJodHRwOi8vc2NoZW1hcy5taWNyb3NvZnQuY29tL3dzLzIwMDgvMDYvaWRlbnRpdHkvY2xhaW1zL3JvbGUiOiJPUFIiLCJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9uYW1laWRlbnRpZmllciI6IlNTT1RFU1QiLCJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9lbWFpbGFkZHJlc3MiOiJ0YXBhbi5jaGFrcmFib3J0eUBwYXJ0bmVycy50YXRhc3RlZWwuY29tIiwiZXhwIjoxNzY0ODQ4Njc5LCJpc3MiOiJUU1VJU0wgRW1wbG95ZWUgc2VsZiBoZWxwIiwiYXVkIjoiVFNVSVNMIG9wciBhbmQgbm9uIG9wcyBlbXBsb3llZXMifQ.RkYehlgGJalTBKyqp7p2H0ssxpCUu67ZPOct4C8vrwY";

        protected void Page_Load(object sender, EventArgs e)
        {
            Response.Write("<b>DEBUG MODE ENABLED</b><br/><br/>");

            try
            {
                string configToken = ConfigurationManager.AppSettings["AccessToken"];
                Response.Write("Config Token: " + configToken + "<br/>");

        

             
                string hardcodedToken = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9uYW1lIjoiVEFQQU4gQ0hBS1JBQk9SVFkiLCJodHRwOi8vc2NoZW1hcy5taWNyb3NvZnQuY29tL3dzLzIwMDgvMDYvaWRlbnRpdHkvY2xhaW1zL3JvbGUiOiJPUFIiLCJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9uYW1laWRlbnRpZmllciI6IlNTT1RFU1QiLCJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9lbWFpbGFkZHJlc3MiOiJ0YXBhbi5jaGFrcmFib3J0eUBwYXJ0bmVycy50YXRhc3RlZWwuY29tIiwiZXhwIjoxNzY0ODQ4Njc5LCJpc3MiOiJUU1VJU0wgRW1wbG95ZWUgc2VsZiBoZWxwIiwiYXVkIjoiVFNVSVNMIG9wciBhbmQgbm9uIG9wcyBlbXBsb3llZXMifQ.RkYehlgGJalTBKyqp7p2H0ssxpCUu67ZPOct4C8vrwY"; 

                if (!string.IsNullOrEmpty(_Default.HardcodedJWT))
                {
                    Response.Write("<span style='color:green'>Using HARD-CODED token.</span><br/><br/>");
                    DecodeAndSetSession(_Default.HardcodedJWT);
                    return;
                }


                HttpCookie authCookie = Request.Cookies["accessToken"];

                if (authCookie == null)
                {
                    Response.Write("<span style='color:red'>Cookie 'accessToken' not found!</span>");
                    return;
                }

                if (string.IsNullOrEmpty(authCookie.Value))
                {
                    Response.Write("<span style='color:red'>Cookie value is EMPTY!</span>");
                    return;
                }

                string cookieToken = authCookie.Value;
                Response.Write("Cookie Token: <br/>" + cookieToken + "<br/><br/>");


                if (cookieToken != configToken)
                {
                    Response.Write("<span style='color:red'>Token mismatch!<br/>Cookie Token != Config Token</span>");
                    return;
                }

                DecodeAndSetSession(cookieToken);
            }
            catch (Exception ex)
            {
                Response.Write($"<span style='color:red'><b>Exception:</b> {ex.Message}</span><br/>");
                if (ex.InnerException != null)
                    Response.Write("<br/>Inner: " + ex.InnerException.Message);
            }
        }


        private void DecodeAndSetSession(string jwtToken)
        {
            Response.Write("<b>Decoding JWT…</b><br/><br/>");

            var handler = new JwtSecurityTokenHandler();
            var token = handler.ReadJwtToken(jwtToken);

            string userName = token.Claims
                .FirstOrDefault(c => c.Type == "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name")
                ?.Value;

            string userId = token.Claims
                .FirstOrDefault(c => c.Type == "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier")
                ?.Value;

            string email = token.Claims
                .FirstOrDefault(c => c.Type == "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress")
                ?.Value;

            Session["UserName"] = userName;
            Session["UserId"] = userId;
            Session["UserEmail"] = email;

            //UIHelper.LoadUserPermission(Membership.GetUser("151514").ProviderUserKey.ToString());

            Response.Write("<b>Decoded Claims:</b><br/>");
            Response.Write("Name: " + userName + "<br/>");
            Response.Write("User ID: " + userId + "<br/>");
            Response.Write("Email: " + email + "<br/><br/>");
        }
    
}


   protected override void OnLoad(EventArgs e)
   {
       base.OnLoad(e);

       // Check if session exists
       if (Session["UserId"] == null || Session["UserName"] == null)
       {
           // Try to rebuild session using JWT
           JwtSessionHelper.RestoreSessionFromJWT();

           if (Session["UserId"] == null)  // still null → no auth
           {
               Response.Redirect("~/Account/AccessDenied.aspx");
               return;
           }
       }

       // OPTIONAL: Load permissions if needed
       //UIHelper.LoadUserPermission(Session["UserId"].ToString());
   }


 public static class JwtSessionHelper
 {
     public static void RestoreSessionFromJWT()
     {
         try
         {
             // 1️⃣ Hardcoded token first (debug mode)
             string hardcodedToken = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9uYW1lIjoiVEFQQU4gQ0hBS1JBQk9SVFkiLCJodHRwOi8vc2NoZW1hcy5taWNyb3NvZnQuY29tL3dzLzIwMDgvMDYvaWRlbnRpdHkvY2xhaW1zL3JvbGUiOiJPUFIiLCJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9uYW1laWRlbnRpZmllciI6IlNTT1RFU1QiLCJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9lbWFpbGFkZHJlc3MiOiJ0YXBhbi5jaGFrcmFib3J0eUBwYXJ0bmVycy50YXRhc3RlZWwuY29tIiwiZXhwIjoxNzY0ODQ4Njc5LCJpc3MiOiJUU1VJU0wgRW1wbG95ZWUgc2VsZiBoZWxwIiwiYXVkIjoiVFNVSVNMIG9wciBhbmQgbm9uIG9wcyBlbXBsb3llZXMifQ.RkYehlgGJalTBKyqp7p2H0ssxpCUu67ZPOct4C8vrwY";
             string jwtToken = null;

             if (!string.IsNullOrEmpty(hardcodedToken))
             {
                 jwtToken = hardcodedToken;
             }
             else
             {
                 // 2️⃣ Cookie fallback
                 HttpCookie cookie = HttpContext.Current.Request.Cookies["accessToken"];
                 if (cookie != null && !string.IsNullOrEmpty(cookie.Value))
                     jwtToken = cookie.Value;
             }

             if (jwtToken == null) return;

             var handler = new JwtSecurityTokenHandler();
             var token = handler.ReadJwtToken(jwtToken);

             HttpContext.Current.Session["UserName"] = token.Claims
                 .FirstOrDefault(c => c.Type.Contains("/claims/name"))?.Value;

             HttpContext.Current.Session["UserId"] = token.Claims
                 .FirstOrDefault(c => c.Type.Contains("/claims/nameidentifier"))?.Value;

             HttpContext.Current.Session["UserEmail"] = token.Claims
                 .FirstOrDefault(c => c.Type.Contains("/claims/emailaddress"))?.Value;
         }
         catch
         {
             // ignore
         }
     }
 }
