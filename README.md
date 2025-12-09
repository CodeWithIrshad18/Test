var claims = new List<Claim>
{
    new Claim(ClaimTypes.NameIdentifier, user.Id.ToString()),
    new Claim(ClaimTypes.Name, user.UserName),
    new Claim(ClaimTypes.Role, user.Role)
};

var claimsIdentity = new ClaimsIdentity(claims, CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity));




using Microsoft.AspNetCore.Authentication.Cookies;
using Microsoft.AspNetCore.Authorization;
using Microsoft.EntityFrameworkCore;
using PowerShutdown.Models;
using PowerShutdown.PasswordHasher;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllersWithViews();
var Provider = builder.Services.BuildServiceProvider();
var Config = Provider.GetRequiredService<IConfiguration>();
builder.Services.AddDbContext<PSDBContext>(item => item.UseSqlServer(Config.GetConnectionString("PSDBCs")));
builder.Services.AddTransient<Hash_Password>();
builder.Services.AddHttpContextAccessor();
builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.LoginPath = "/User/Login";
        options.AccessDeniedPath = "/PS/AccessDenied";
    });
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("CanWrite", policy => policy.Requirements.Add(new PermissionRequirement("AllowWrite")));
    options.AddPolicy("CanRead", policy => policy.Requirements.Add(new PermissionRequirement("AllowRead")));
    options.AddPolicy("CanModify", policy => policy.Requirements.Add(new PermissionRequirement("AllowModify")));
    options.AddPolicy("CanDelete", policy => policy.Requirements.Add(new PermissionRequirement("AllowDelete")));
});
builder.Services.AddTransient<IAuthorizationHandler, PermissionHandler>();
builder.Services.AddSession(options =>
{
    options.IdleTimeout = TimeSpan.FromMinutes(30);
    options.Cookie.HttpOnly = true;
    options.Cookie.IsEssential = true;
});

var app = builder.Build();

if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Home/Error");
    app.UseHsts();
}

app.UseHttpsRedirection();
app.UseStaticFiles();
app.UseSession();
app.UseRouting();
app.UseAuthentication();
app.UseAuthorization();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=User}/{action=Login}/{id?}");

app.Run();


  public class PermissionHandler : AuthorizationHandler<PermissionRequirement>
  {
      private readonly PSDBContext _context;
      private readonly IHttpContextAccessor _httpContextAccessor;

      public PermissionHandler(PSDBContext context, IHttpContextAccessor httpContextAccessor)
      {
          _context = context;
          _httpContextAccessor = httpContextAccessor;
      }

      protected override async Task HandleRequirementAsync(AuthorizationHandlerContext context, PermissionRequirement requirement)
      {
          var httpContext = _httpContextAccessor.HttpContext;

          if (httpContext == null || !httpContext.User.Identity.IsAuthenticated)
          {
              return;
          }

         
          var userIdString = httpContext.Session.GetString("Session");
          if (string.IsNullOrEmpty(userIdString))
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
  }


from this line it is returned 
if (httpContext == null || !httpContext.User.Identity.IsAuthenticated)
          {
              return;
          }
