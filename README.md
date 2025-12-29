curl https://servicesdev.tusil.co.in/RS_BarricadingAPI/api/Auth/login -k -v

curl --tlsv1.2 -X OPTIONS https://servicesdev.tusil.co.in/RS_BarricadingAPI/api/Auth/login -k -v



builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowAll",
        policy =>
        {
            policy
                .AllowAnyOrigin()
                .AllowAnyMethod()
                .AllowAnyHeader();
        });
});

var app = builder.Build();

if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();

app.UseRouting();

// ✅ CORS MUST be here
app.UseCors("AllowAll");

// ✅ Allow OPTIONS before auth
app.UseAuthentication();
app.UseAuthorization();

// ❌ Move custom middleware AFTER CORS
app.UseMiddleware<SecretKeyAuthorization>();

app.MapControllers();

app.Run();


public class SecretKeyAuthorization
{
    private readonly RequestDelegate _next;

    public SecretKeyAuthorization(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        // ✅ Allow preflight request
        if (context.Request.Method == HttpMethods.Options)
        {
            context.Response.StatusCode = StatusCodes.Status204NoContent;
            return;
        }

        // Your existing logic here
        // Example:
        // var secretKey = context.Request.Headers["X-Secret-Key"];
        // if (string.IsNullOrEmpty(secretKey)) { ... }

        await _next(context);
    }
}

curl -X OPTIONS https://servicesdev.tusil.co.in/RS_BarricadingAPI/api/Auth/login -i




using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.IdentityModel.Tokens;
using RoadSideBarricading_API.DataAcess;
using RoadSideBarricading_API.Middleware;
using RoadSideBarricading_API.Services;
using System.Text;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.

builder.Services.AddControllers();
// Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var Config = builder.Configuration;
var connectionString = Config.GetConnectionString("Dbcs");
var environment = builder.Environment;
builder.Services.AddSingleton(new RSBDataAcess(connectionString));
builder.Services.AddHttpContextAccessor();

builder.Services.AddSingleton<TokenService>();

builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        var jwtSettings = builder.Configuration.GetSection("JwtSettings");
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = jwtSettings["Issuer"],
            ValidAudience = jwtSettings["Audience"],
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(jwtSettings["Key"]))
        };
    });

builder.Services.AddAuthorization();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();
app.UseMiddleware<SecretKeyAuthorization>();
app.UseAuthentication();

app.UseAuthorization();

app.MapControllers();

app.Run();
