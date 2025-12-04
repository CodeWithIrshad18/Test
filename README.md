using System;
using System.Configuration;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

public partial class _Default : Page
{
    protected void Page_Load(object sender, EventArgs e)
    {
        Response.Write("<b>DEBUG MODE ENABLED</b><br/><br/>");

        try
        {
            // ==============================
            // 1. Load Access Token from Web.Config
            // ==============================
            string configToken = ConfigurationManager.AppSettings["AccessToken"];
            Response.Write("Config Token: " + configToken + "<br/>");

            // ==============================
            // 2. Use HARD-CODED token for debugging
            // ==============================
#if DEBUG
            // INSERT YOUR TEST JWT HERE ↓↓↓
            string hardcodedToken = ""; // <-- put your JWT string here

            if (!string.IsNullOrEmpty(hardcodedToken))
            {
                Response.Write("<span style='color:green'>Using HARD-CODED token.</span><br/><br/>");
                DecodeAndSetSession(hardcodedToken);
                return;
            }
#endif

            // ==============================
            // 3. Read token from cookie
            // ==============================
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

            // ==============================
            // 4. Compare cookie token with config token
            // ==============================
            if (cookieToken != configToken)
            {
                Response.Write("<span style='color:red'>Token mismatch!<br/>Cookie Token != Config Token</span>");
                return;
            }

            // ==============================
            // 5. Decode JWT + Store in Session
            // ==============================
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

        Response.Write("<b>Decoded Claims:</b><br/>");
        Response.Write("Name: " + userName + "<br/>");
        Response.Write("User ID: " + userId + "<br/>");
        Response.Write("Email: " + email + "<br/><br/>");
    }
}

  
  
  
  
protected void Page_Load(object sender, EventArgs e)
  {
     
      string configToken = ConfigurationManager.AppSettings["AccessToken"];

      
      HttpCookie authCookie = Request.Cookies["accessToken"];


      if (authCookie == null || string.IsNullOrEmpty(authCookie.Value))
      {
          Response.Redirect("~/Account/AccessDenied.aspx");
          return;
      }

      string cookieToken = authCookie.Value;

      if (cookieToken != configToken)
      {
          Response.Redirect("~/Account/AccessDenied.aspx");
          return;
      }


      string jwtToken = authCookie.Value;

   
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

      
       Response.Write($"{userName} | {userId} | {email}");



  }
