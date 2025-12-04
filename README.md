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
