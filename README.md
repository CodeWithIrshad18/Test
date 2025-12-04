<appSettings>
   <add key="MasterAccessToken" value="ABC123" />
</appSettings>

protected void Page_Load(object sender, EventArgs e)
{
    // Get token from Web.config
    string configToken = ConfigurationManager.AppSettings["MasterAccessToken"];

    // Try to read cookie
    HttpCookie authCookie = Request.Cookies["AccessToken"];

    // Validate
    if (authCookie == null || string.IsNullOrEmpty(authCookie.Value))
    {
        Response.Redirect("AccessDenied.aspx");
        return;
    }

    string cookieToken = authCookie.Value;

    if (cookieToken != configToken)
    {
        Response.Redirect("AccessDenied.aspx");
        return;
    }

    // ✔ Token matched — allow page
}



-----------------------------secret key and cookies-----------------------------
  var claims = new[]
    {
          new Claim(ClaimTypes.Name, name),
          new Claim(ClaimTypes.Role, role),
          new Claim(ClaimTypes.NameIdentifier, pno),
          new Claim(ClaimTypes.Email, string.IsNullOrEmpty(email) ? "":email)
     };
  var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_jwtSettings.SecretKey));
  var creds = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);

  var token = new JwtSecurityToken(
      issuer: _jwtSettings.Issuer,
      audience: _jwtSettings.Audience,
      claims: claims,
      expires: DateTime.UtcNow.AddHours(1),
      signingCredentials: creds
  );
  var jwtToken = new JwtSecurityTokenHandler().WriteToken(token);
  Response.Cookies.Append("accessToken", jwtToken, new CookieOptions
  {
      HttpOnly = true,
      Secure = true,
      //SameSite = SameSiteMode.Lax,
      SameSite = SameSiteMode.None,   // required for cross-app cookies
      //Domain = "servicesdev.tsuisl.co.in",
      Path = "/",
      Expires = DateTimeOffset.UtcNow.AddHours(1)
  });

---------------------------program.cs------------------------------------------


// -------------------- Authentication (SSO Enabled) --------------------
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = jwtSettings.Issuer,
            ValidAudience = jwtSettings.Audience,
            IssuerSigningKey = new SymmetricSecurityKey(key),
            RoleClaimType = ClaimTypes.Role
        };

        // Read token from Shared Cookie (SSO Cookie)
        options.Events = new JwtBearerEvents
        {
            OnMessageReceived = context =>
            {
                if (context.Request.Cookies.TryGetValue("accessToken", out var token))
                {
                    context.Token = token;
                }
                return Task.CompletedTask;
            }
        };
    });

// -------------------- Cookie Settings (Shared SSO Cookie) --------------------
builder.Services.PostConfigure<CookiePolicyOptions>(options =>
{
    // options.MinimumSameSitePolicy = SameSiteMode.Lax; // MUST for SSO
    options.MinimumSameSitePolicy = SameSiteMode.None;
});

// -------------------- MVC + Services --------------------

