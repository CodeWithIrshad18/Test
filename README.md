using GFAS.Email;
using GFAS.Models;
using GFAS.PasswordHasher;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.FileProviders;
using System.Security.Claims;

var builder = WebApplication.CreateBuilder(args);


builder.Services.AddControllersWithViews();
var Provider = builder.Services.BuildServiceProvider();
var Config = Provider.GetRequiredService<IConfiguration>();
builder.Services.AddDbContext<INNOVATIONDBContext>(item => item.UseSqlServer(Config.GetConnectionString("LoginDB")));
builder.Services.AddDbContext<UserLoginDBContext>(item => item.UseSqlServer(Config.GetConnectionString("UserLoginDB")));
builder.Services.AddDbContext<TSUISLRFIDDBContext>(item => item.UseSqlServer(Config.GetConnectionString("RFID")));

builder.Services.AddTransient<Hash_Password>();
builder.Services.AddTransient<EmailService>();
builder.Services.AddScoped<IPermissionService, PermissionService>();
builder.Services.AddHttpContextAccessor();
builder.Services.AddMemoryCache();
builder.Services.AddAuthentication("Cookies")
.AddCookie("Cookies", options =>
{
    options.LoginPath = "/User/Login";
    options.ExpireTimeSpan = TimeSpan.FromDays(365);
    options.SlidingExpiration = true;
});
builder.Services.AddAuthorization();

var app = builder.Build();


if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Home/Error");
   
    app.UseHsts();
}

app.Use(async (context, next) =>
{
    if (!context.User.Identity.IsAuthenticated)
    {
        var userId = context.Request.Cookies["Session"];
        var UserName = context.Request.Cookies["UserName"];
        var Pno = context.Request.Cookies["UserSession"];
        if (!string.IsNullOrEmpty(userId) && !string.IsNullOrEmpty(UserName) && !string.IsNullOrEmpty(Pno))
        {
            var claims = new List<Claim>
            {
                new Claim(ClaimTypes.Name,UserName),
                new Claim("Pno",Pno),
                new Claim("Session",userId)
            };

            var identity = new ClaimsIdentity(claims, "Cookies");
            var principal = new ClaimsPrincipal(identity);
            context.User = principal;

        }
    }
        await next();
});

app.UseHttpsRedirection();
app.UseStaticFiles();
app.UseStaticFiles(new StaticFileOptions
{
    ServeUnknownFileTypes = true, // VERY IMPORTANT
    DefaultContentType = "application/octet-stream"
    

});

app.UseRouting();
app.UseAuthentication();
app.UseAuthorization();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Face}/{action=GeoFencing}/{id?}");

app.Run();
