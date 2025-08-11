[HttpPost]
public async Task<IActionResult> Login(AppLogin login)
{
    if (!string.IsNullOrEmpty(login.UserId) && string.IsNullOrEmpty(login.Password))
    {
        ViewBag.FailedMsg = "Login Failed: Password is required";
        return View(login);
    }

    var user = await context.AppLogins
        .Where(x => x.UserId == login.UserId)
        .FirstOrDefaultAsync();

    if (user != null)
    {
        bool isPasswordValid = hash_Password.VerifyPassword(login.Password, user.Password, user.PasswordSalt);

        if (isPasswordValid)
        {
            // 🔹 STEP 1: Get device ID from request (query param, form, or header)
            string deviceId = Request.Form["DeviceID"]; // If coming from form
            // Or: string deviceId = Request.Headers["Device-ID"];
            // Or: string deviceId = login.DeviceID; // if you add to AppLogin model

            if (string.IsNullOrEmpty(deviceId))
            {
                ViewBag.FailedMsg = "Device ID is required for login.";
                return View(login);
            }

            // 🔹 STEP 2: Check if device already linked
            var existingDevice = await context.AppDeviceDetails
                .FirstOrDefaultAsync(d => d.UserId == login.UserId);

            if (existingDevice == null)
            {
                // First login → Save device ID
                var newDevice = new AppDeviceDetails
                {
                    Id = Guid.NewGuid(),
                    UserId = login.UserId,
                    DeviceID = deviceId,
                    DeviceModel = Request.Form["DeviceModel"], // optional
                    DeviceIDCount = 1
                };

                await context.AppDeviceDetails.AddAsync(newDevice);
                await context.SaveChangesAsync();
            }
            else if (existingDevice.DeviceID != deviceId)
            {
                // Device mismatch → Reject
                ViewBag.FailedMsg = "Your account is already linked to another device.";
                return View(login);
            }

            // 🔹 STEP 3: Get employee details
            string query = @"
                SELECT EMA_PERNO, EMA_ENAME 
                FROM SAPHRDB.dbo.T_Empl_All 
                WHERE EMA_PERNO = @Pno";

            var parameters = new { Pno = login.UserId };
            EmpDTO userLoginData;

            using (var connection = GetRFIDConnectionString())
            {
                await connection.OpenAsync();
                userLoginData = await connection.QueryFirstOrDefaultAsync<EmpDTO>(query, parameters);
            }

            string userName = userLoginData?.EMA_ENAME ?? "Guest";
            string userPno = userLoginData?.EMA_PERNO ?? "N/A";

            HttpContext.Session.SetString("Session", userPno);
            HttpContext.Session.SetString("UserName", userName);
            HttpContext.Session.SetString("UserSession", login.UserId);

            var cookieOptions = new CookieOptions
            {
                Expires = DateTimeOffset.Now.AddYears(1),
                HttpOnly = false,
                Secure = true,
                IsEssential = true
            };

            Response.Cookies.Append("UserSession", login.UserId, cookieOptions);
            Response.Cookies.Append("Session", userPno, cookieOptions);
            Response.Cookies.Append("UserName", userName, cookieOptions);

            return RedirectToAction("GeoFencing", "Geo");
        }
        else
        {
            ViewBag.FailedMsg = "Login Failed: Incorrect password";
        }
    }
    else
    {
        ViewBag.FailedMsg = "Login Failed: User not found";
    }

    return View(login);
}




string deviceId = login.DeviceID;

var existingDevice = await context.AppDeviceDetails
    .FirstOrDefaultAsync(d => d.UserId == login.UserId);

if (existingDevice == null)
{
    // First login → Save device ID
    var newDevice = new AppDeviceDetails
    {
        Id = Guid.NewGuid(),
        UserId = login.UserId,
        DeviceID = deviceId,
        DeviceModel = login.DeviceModel,
        DeviceIDCount = 1
    };

    await context.AppDeviceDetails.AddAsync(newDevice);
    await context.SaveChangesAsync();
}
else if (existingDevice.DeviceID != deviceId)
{
    ViewBag.FailedMsg = "Your account is already linked to another device.";
    return View(login);
}



<form asp-action="Login" method="post">
    <input type="text" name="UserId" placeholder="User ID" required />
    <input type="password" name="Password" placeholder="Password" required />

    <!-- Hidden fields for device info -->
    <input type="hidden" id="DeviceID" name="DeviceID" />
    <input type="hidden" id="DeviceModel" name="DeviceModel" />

    <button type="submit">Login</button>
</form>

<script>
    // Generate a simple device ID if not already stored
    function getOrCreateDeviceID() {
        let deviceId = localStorage.getItem("DeviceID");
        if (!deviceId) {
            deviceId = crypto.randomUUID(); // browser unique ID
            localStorage.setItem("DeviceID", deviceId);
        }
        return deviceId;
    }

    // Get browser/device info
    function getDeviceModel() {
        return navigator.userAgent; // basic device/browser string
    }

    document.getElementById("DeviceID").value = getOrCreateDeviceID();
    document.getElementById("DeviceModel").value = getDeviceModel();
</script>




this is my login part 

 [HttpPost]
 public async Task<IActionResult> Login(AppLogin login)
 {
     if (!string.IsNullOrEmpty(login.UserId) && string.IsNullOrEmpty(login.Password))
     {
         ViewBag.FailedMsg = "Login Failed: Password is required";
         return View(login);
     }

     var user = await context.AppLogins
         .Where(x => x.UserId == login.UserId)
         .FirstOrDefaultAsync();

     if (user != null)
     {
         bool isPasswordValid = hash_Password.VerifyPassword(login.Password, user.Password, user.PasswordSalt);

         if (isPasswordValid)
         {

             string query = @"
         SELECT EMA_PERNO, EMA_ENAME 
         FROM SAPHRDB.dbo.T_Empl_All 
         WHERE EMA_PERNO = @Pno";

             var parameters = new { Pno = login.UserId };

             EmpDTO userLoginData;

             using (var connection = GetRFIDConnectionString())
             {
                 await connection.OpenAsync();
                 userLoginData = await connection.QueryFirstOrDefaultAsync<EmpDTO>(query, parameters);
             }

             string userName = userLoginData?.EMA_ENAME ?? "Guest";
             string userPno = userLoginData?.EMA_PERNO ?? "N/A";


             HttpContext.Session.SetString("Session", userPno);
             HttpContext.Session.SetString("UserName", userName);
             HttpContext.Session.SetString("UserSession", login.UserId);

             // Set cookies
             var cookieOptions = new CookieOptions
             {
                 Expires = DateTimeOffset.Now.AddYears(1),
                 HttpOnly = false,
                 Secure = true,
                 IsEssential = true
             };

             Response.Cookies.Append("UserSession", login.UserId, cookieOptions);
             Response.Cookies.Append("Session", userPno, cookieOptions);
             Response.Cookies.Append("UserName", userName, cookieOptions);



             return RedirectToAction("GeoFencing", "Geo");
         }
         else
         {
             ViewBag.FailedMsg = "Login Failed: Incorrect password";
         }
     }
     else
     {
         ViewBag.FailedMsg = "Login Failed: User not found";
     }

     return View(login);


 }

and this Is my device details model which stores device id
 public class AppDeviceDetails
 {
     public Guid Id { get; set; }
     public string? UserId { get; set; }
     public string? DeviceModel { get; set; }
     public string? DeviceID { get; set; }
     public int DeviceIDCount { get; set; }
 }
