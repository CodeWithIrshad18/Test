<input type="text" class="inputField" id="ADID" placeholder="ADID">
<input type="password" class="inputField" id="password" placeholder="Password">
<button type="button" id="btnLogin">Login</button>

<div id="message" style="color:red; margin-top:10px;"></div>

<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
<script>
    $("#btnLogin").click(function () {
        var adid = $("#ADID").val();
        var password = $("#password").val();

        if (!adid || !password) {
            $("#message").text("Please enter ADID and password.");
            return;
        }

        $.ajax({
            url: '/YourControllerName/Login',
            type: 'POST',
            contentType: 'application/json',
            data: JSON.stringify({ ADID: adid, Password: password }),
            success: function (response) {
                if (response.success) {
                    window.location.href = '/Home/Index'; // redirect on success
                } else {
                    $("#message").text("Login failed. Invalid credentials.");
                }
            },
            error: function () {
                $("#message").text("Error contacting server.");
            }
        });
    });
</script>

using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Newtonsoft.Json;

public class AccountController : Controller
{
    [HttpPost]
    public async Task<IActionResult> Login([FromBody] AppLogin appLogin)
    {
        if (appLogin == null || string.IsNullOrEmpty(appLogin.ADID) || string.IsNullOrEmpty(appLogin.Password))
            return Json(new { success = false, message = "Missing credentials" });

        string apiUrl = "https://servicesdev.tsuisl.co.in/ADService/API/ADID/ADService";

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("XApiKey", "your-xapikey-value");
            client.DefaultRequestHeaders.Add("CityGis", "your-citygis-value");

            // Example: sending ADID and password as JSON
            var json = JsonConvert.SerializeObject(new
            {
                Domain = "tatasteel",
                ADID = appLogin.ADID,
                Password = appLogin.Password
            });

            var content = new StringContent(json, System.Text.Encoding.UTF8, "application/json");

            var response = await client.PostAsync(apiUrl, content);
            if (!response.IsSuccessStatusCode)
                return Json(new { success = false, message = "API call failed" });

            var result = await response.Content.ReadAsStringAsync();

            bool apiResult = false;
            bool.TryParse(result, out apiResult); // expects "true" or "false"

            if (apiResult)
                return Json(new { success = true });
            else
                return Json(new { success = false, message = "Invalid login" });
        }
    }
}

public class AppLogin
{
    public string ADID { get; set; }
    public string Password { get; set; }
}




https://servicesdev.tsuisl.co.in/ADService/API/ADID/ADService"
Domain = "tatasteel",
Headers : XApiKey, CityGis#123


[HttpPost]

public IActionResult Login(AppLogin appLogin)
{
    return View();
}


<input type="text" class="inputField" id="ADID" placeholder="ADID">

 <input type="password" class="inputField" id="password" placeholder="Password">
