    public partial class _Default : Classes.basePage 
    {
        protected void Page_Load(object sender, EventArgs e)
        {
            Response.Write("<b>DEBUG MODE ENABLED</b><br/><br/>");

            try
            {
                string configToken = ConfigurationManager.AppSettings["AccessToken"];
                Response.Write("Config Token: " + configToken + "<br/>");

        

             
                string hardcodedToken = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9uYW1lIjoiVEFQQU4gQ0hBS1JBQk9SVFkiLCJodHRwOi8vc2NoZW1hcy5taWNyb3NvZnQuY29tL3dzLzIwMDgvMDYvaWRlbnRpdHkvY2xhaW1zL3JvbGUiOiJPUFIiLCJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9uYW1laWRlbnRpZmllciI6IlNTT1RFU1QiLCJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9lbWFpbGFkZHJlc3MiOiJ0YXBhbi5jaGFrcmFib3J0eUBwYXJ0bmVycy50YXRhc3RlZWwuY29tIiwiZXhwIjoxNzY0ODQ4Njc5LCJpc3MiOiJUU1VJU0wgRW1wbG95ZWUgc2VsZiBoZWxwIiwiYXVkIjoiVFNVSVNMIG9wciBhbmQgbm9uIG9wcyBlbXBsb3llZXMifQ.RkYehlgGJalTBKyqp7p2H0ssxpCUu67ZPOct4C8vrwY"; 

                if (!string.IsNullOrEmpty(hardcodedToken))
                {
                    Response.Write("<span style='color:green'>Using HARD-CODED token.</span><br/><br/>");
                    DecodeAndSetSession(hardcodedToken);
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


 public class AuthHelper : System.Web.UI.Page
 {
     protected void CheckLogin(EventArgs e)
     {
         

         FormsIdentity formsIdentity = HttpContext.Current.User.Identity as FormsIdentity;
        
             //FormsAuthenticationTicket ticket = FormsAuthentication.Decrypt(reqCookies.Value);
             //FormsAuthenticationTicket ticket = formsIdentity.Ticket;
             //if (Membership.ValidateUser(HttpContext.Current.User.Identity.Name, ticket.UserData.ToString()))
             //{

             if (HttpContext.Current.User.Identity.IsAuthenticated) { 


                 Session["UserName"] = Membership.GetUser(HttpContext.Current.User.Identity.Name).UserName.ToString();
                 Session["UserID"] = Membership.GetUser(HttpContext.Current.User.Identity.Name).ProviderUserKey.ToString();
                 Session["Email"] = Membership.GetUser(HttpContext.Current.User.Identity.Name).Email.ToString();
                 Session["initial"] = "";
                 Session["UName"] = Membership.GetUser(HttpContext.Current.User.Identity.Name).PasswordQuestion.ToString();

                 Session["CookieEnabled"] = true;

                 //FormsAuthentication.SetAuthCookie(Session["UserName"].ToString(), false);
                 UIHelper.LoadUserPermission(Membership.GetUser(HttpContext.Current.User.Identity.Name).ProviderUserKey.ToString());

                 //FormsAuthentication.RedirectFromLoginPage(HttpContext.Current.User.Identity.Name, true);
                 //FormsAuthentication.GetRedirectUrl(HttpContext.Current.User.Identity.Name, true);
                 //Server.Transfer(HttpContext.Current.Request.Url.AbsolutePath);

                 if (((DataSet)Session["USER_PERMISSION"]).Tables[0].Rows.Count == 0)
                     Response.Redirect(Classes.Urls.RedirectUrl);

                 if (HttpContext.Current.Request.QueryString != null && HttpContext.Current.Request.QueryString.Count > 0)
                     Response.Redirect(Request.QueryString["ReturnUrl"]);
                 //else
                 //{
                 //    Response.Redirect("~/Default.aspx");
                 //}


                 // Membership.ValidateUser(HttpContext.Current.User.Identity.Name,"jusco@12");
             }else
                 Response.Redirect(Classes.Urls.RedirectUrl + "/Account/Login.aspx?ReturnUrl=" + HttpContext.Current.Request.Url);
         
         

     }
 }
