using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Web;
using System.Web.Security;

public class Global : System.Web.HttpApplication
{
    // 🔥 Hardcoded JWT for debugging
    public static string HardcodedJWT = "PASTE_YOUR_JWT_TOKEN_HERE";

    void Application_Start(object sender, EventArgs e)
    {
        if (Membership.GetUser("tapan") == null)
        {
            Membership.CreateUser("tapan", "tapan123", "tapan@mail.com");
        }

        DataHelper.ConnectionString =
            System.Web.Configuration.WebConfigurationManager
            .ConnectionStrings["ApplicationServices"].ConnectionString;
    }

    void Application_End(object sender, EventArgs e) { }
    void Application_Error(object sender, EventArgs e) { }
    void Session_Start(object sender, EventArgs e) { }
    void Session_End(object sender, EventArgs e) { }

    // ====================================================================
    //   MAIN FUNCTION: Restore Session on every request
    // ====================================================================
    void Application_AcquireRequestState(object sender, EventArgs e)
    {
        try
        {
            // If session already restored → do nothing
            if (HttpContext.Current.Session["UserName"] != null)
                return;

            string jwtToken = null;

            // 1️⃣ FIRST priority → Hardcoded JWT (for debugging)
            if (!string.IsNullOrEmpty(HardcodedJWT))
            {
                jwtToken = HardcodedJWT;
            }
            else
            {
                // 2️⃣ SECOND priority → Cookie token
                HttpCookie cookie = HttpContext.Current.Request.Cookies["accessToken"];
                if (cookie != null && !string.IsNullOrEmpty(cookie.Value))
                    jwtToken = cookie.Value;
            }

            // If still no token → nothing to restore
            if (string.IsNullOrEmpty(jwtToken))
                return;

            // Decode JWT
            var handler = new JwtSecurityTokenHandler();
            var token = handler.ReadJwtToken(jwtToken);

            // Extract claims → save to Session
            HttpContext.Current.Session["UserName"] = token.Claims
                .FirstOrDefault(c => c.Type ==
                  "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name")?.Value;

            HttpContext.Current.Session["UserId"] = token.Claims
                .FirstOrDefault(c => c.Type ==
                  "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier")?.Value;

            HttpContext.Current.Session["UserEmail"] = token.Claims
                .FirstOrDefault(c => c.Type ==
                  "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress")?.Value;
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine("JWT Restore Error: " + ex.Message);
        }
    }
}




public class Global : System.Web.HttpApplication
{
    void Application_Start(object sender, EventArgs e)
    {
        if (Membership.GetUser("tapan") == null)
        {
            MembershipUser usr;
            usr = Membership.CreateUser("tapan", "tapan123", "tapan@mail.com");
        }

        DataHelper.ConnectionString =
            System.Web.Configuration.WebConfigurationManager
            .ConnectionStrings["ApplicationServices"].ConnectionString;
    }

    void Application_End(object sender, EventArgs e) { }

    void Application_Error(object sender, EventArgs e) { }

    void Session_Start(object sender, EventArgs e) { }

    void Session_End(object sender, EventArgs e) { }

    // ⬇⬇ ADD THIS METHOD ⬇⬇
    void Application_AcquireRequestState(object sender, EventArgs e)
    {
        try
        {
            if (HttpContext.Current.Session["UserName"] != null)
                return;

            HttpCookie cookie = HttpContext.Current.Request.Cookies["accessToken"];
            if (cookie == null || string.IsNullOrEmpty(cookie.Value))
                return;

            string jwtToken = cookie.Value;

            var handler = new System.IdentityModel.Tokens.Jwt.JwtSecurityTokenHandler();
            var token = handler.ReadJwtToken(jwtToken);

            HttpContext.Current.Session["UserName"] = token.Claims
                .FirstOrDefault(c => c.Type ==
                  "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name")?.Value;

            HttpContext.Current.Session["UserId"] = token.Claims
                .FirstOrDefault(c => c.Type ==
                  "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier")?.Value;

            HttpContext.Current.Session["UserEmail"] = token.Claims
                .FirstOrDefault(c => c.Type ==
                  "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress")?.Value;
        }
        catch { }
    }
}

  
  
  
  public class Global : System.Web.HttpApplication
  {

      void Application_Start(object sender, EventArgs e)
      {
          if (Membership.GetUser("tapan") == null)
          {
              MembershipUser usr;
              usr = Membership.CreateUser("tapan", "tapan123", "tapan@mail.com");
          }
          // Code that runs on application startup
          DataHelper.ConnectionString = System.Web.Configuration.WebConfigurationManager.ConnectionStrings["ApplicationServices"].ConnectionString;

      }

      void Application_End(object sender, EventArgs e)
      {
          //  Code that runs on application shutdown

      }

      void Application_Error(object sender, EventArgs e)
      {
          // Code that runs when an unhandled error occurs

      }

      void Session_Start(object sender, EventArgs e)
      {
          // Code that runs when a new session is started

      }

      void Session_End(object sender, EventArgs e)
      {
          // Code that runs when a session ends. 
          // Note: The Session_End event is raised only when the sessionstate mode
          // is set to InProc in the Web.config file. If session mode is set to StateServer 
          // or SQLServer, the event is not raised.

      }

  }
