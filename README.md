var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, userName),
    new Claim("UserId", userPno)
};

var claimsIdentity = new ClaimsIdentity(claims, CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });

protected override async Task HandleRequirementAsync(AuthorizationHandlerContext context, PermissionRequirement requirement)
{
    var httpContext = _httpContextAccessor.HttpContext;

    // 1️⃣ Check for authentication
    if (httpContext == null || !httpContext.User.Identity.IsAuthenticated)
    {
        return;
    }

    // 2️⃣ Get user ID (string, not Guid)
    var userIdString = httpContext.Session.GetString("Session"); 
    if (string.IsNullOrEmpty(userIdString))
    {
        return;
    }

    // 3️⃣ Get form/action name
    string? formName = httpContext.GetRouteData()?.Values["action"]?.ToString();
    if (string.IsNullOrEmpty(formName))
    {
        return;
    }

    // 4️⃣ Get form record
    var form = await _context.AppFormDetails
        .Where(f => f.FormName == formName)
        .Select(f => new { f.Id })
        .FirstOrDefaultAsync();

    if (form == null)
    {
        return;
    }

    Guid formId = form.Id;

    // 5️⃣ Check permission using string UserId
    var hasPermission = await _context.AppUserFormPermissions
        .Where(p => p.UserId == userIdString && p.FormId == formId)
        .AnyAsync(p =>
            (requirement.Permission == "AllowWrite" && p.AllowWrite == true) ||
            (requirement.Permission == "AllowRead" && p.AllowRead == true) ||
            (requirement.Permission == "AllowDelete" && p.AllowDelete == true) ||
            (requirement.Permission == "AllowModify" && p.AllowModify == true)
        );

    if (hasPermission)
    {
        context.Succeed(requirement);
    }
}



previously my id was guid but now it is string 
public partial class AppUserFormPermission
{
    public Guid Id { get; set; }
    public string? UserId { get; set; }
    public Guid FormId { get; set; }
    public bool AllowRead { get; set; }
    public bool AllowWrite { get; set; }
    public bool? AllowDelete { get; set; }
    public bool? AllowAll { get; set; }
    public bool? AllowModify { get; set; }
    public bool DownTime { get; set; }
}


 protected override async Task HandleRequirementAsync(AuthorizationHandlerContext context, PermissionRequirement requirement)
 {
     var httpContext = _httpContextAccessor.HttpContext;

     if (httpContext == null || !httpContext.User.Identity.IsAuthenticated)
     {
         return;
     }

     var userIdString = httpContext.Session.GetString("Session"); 
     if (string.IsNullOrEmpty(userIdString) || !Guid.TryParse(userIdString, out Guid userId))
     {
         return;
     }

   
     string? formName = httpContext.GetRouteData()?.Values["action"]?.ToString();

     if (string.IsNullOrEmpty(formName))
     {
         return;
     }

   
     var form = await _context.AppFormDetails
         .Where(f => f.FormName == formName)
         .Select(f => new { f.Id })
         .FirstOrDefaultAsync();

     if (form == null)
     {
         return;
     }

     Guid formId = form.Id;

    
     var hasPermission = await _context.AppUserFormPermissions
         .Where(p => p.UserId == userId.ToString() && p.FormId == formId)
         .AnyAsync(p =>
             (requirement.Permission == "AllowWrite" && p.AllowWrite == true) ||
             (requirement.Permission == "AllowRead" && p.AllowRead == true) ||
             (requirement.Permission == "AllowDelete" && p.AllowDelete == true) ||
             (requirement.Permission == "AllowModify" && p.AllowModify == true)
         );

     if (hasPermission)
     {
         context.Succeed(requirement);
     }
 }
