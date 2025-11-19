ViewBag.LatestPunchTime = latestPunchTime ?? "";

document.addEventListener("DOMContentLoaded", function () {

    const lastPunch = "@ViewBag.LatestPunchTime";  

    // If no punch today → show UI immediately
    if (!lastPunch || lastPunch.trim() === "") {
        showMainUI();
        OnOff();
        return;
    }

    const today = new Date();
    const [hh, mm] = lastPunch.split(":").map(Number);

    const lastPunchDateTime = new Date(
        today.getFullYear(),
        today.getMonth(),
        today.getDate(),
        hh,
        mm,
        0
    );

    const unlockTime = new Date(lastPunchDateTime.getTime() + 10 * 60000);
    const now = new Date();

    if (now < unlockTime) {
        startCountdown(unlockTime);
    } else {
        showMainUI();
        OnOff();
    }

});




controller code :
 
public IActionResult GeoFencing()
 {
     var userId = HttpContext.Request.Cookies["Session"];
     var userName = HttpContext.Request.Cookies["UserName"];

     if (string.IsNullOrEmpty(userId) || string.IsNullOrEmpty(userName))
     {
         return RedirectToAction("Login", "User");
     }

     ViewBag.UserId = userId;
     ViewBag.UserName = userName;

     var data = GetLocations();

     var pno = userId;
     var currentDate = DateTime.Now.ToString("yyyy-MM-dd");

     string connectionString = GetRFIDConnectionString();

     string countQuery = @"
 SELECT COUNT(*) 
 FROM T_TRBDGDAT_EARS 
 WHERE TRBDGDA_BD_PNO = @Pno 
 AND TRBDGDA_BD_DATE = @CurrentDate";

     int punchCount = 0;

     using (var connection = new SqlConnection(connectionString))
     {
         punchCount = connection.QuerySingleOrDefault<int>(
             countQuery,
             new { Pno = pno, CurrentDate = currentDate }
         );
     }

     int mod = punchCount % 2;
     ViewBag.InOut = mod == 0 ? "I" : "O";


     string latestPunchQuery = @"
 SELECT TOP 1 PDE_PUNCHTIME 
 FROM T_TRPUNCHDATA_EARS 
 WHERE PDE_PSRNO = @Pno 
 AND PDE_PUNCHDATE = @CurrentDate
 ORDER BY PDE_PUNCHTIME DESC";

     string latestPunchTime = null;

     using (var connection = new SqlConnection(connectionString))
     {
         latestPunchTime = connection.QuerySingleOrDefault<string>(
             latestPunchQuery,
             new { Pno = pno, CurrentDate = currentDate }
         );
     }

     ViewBag.LatestPunchTime = latestPunchTime;

     return View();
 }

Js:

<script>
document.addEventListener("DOMContentLoaded", function () {

    const lastPunch = "@ViewBag.LatestPunchTime";  
    if (!lastPunch) return;  

    const today = new Date();
    const [hh, mm] = lastPunch.split(":").map(Number);

    const lastPunchDateTime = new Date(
        today.getFullYear(),
        today.getMonth(),
        today.getDate(),
        hh,
        mm,
        0
    );

    const unlockTime = new Date(lastPunchDateTime.getTime() + 10 * 60000);
    const now = new Date();

    if (now < unlockTime) {
        startCountdown(unlockTime);
    } else {

        showMainUI();
        OnOff(); 
    }

});

function startCountdown(unlockTime) {

    document.getElementById("mainFormContainer").style.display = "none";

    document.getElementById("timerScreen").style.display = "block";

    const timerLabel = document.getElementById("countdownTimer");

    const interval = setInterval(() => {
        const now = new Date();
        const diff = unlockTime - now;

        if (diff <= 0) {
            clearInterval(interval);

            showMainUI();

            OnOff();

            return;
        }

        const minutes = Math.floor(diff / 60000);
        const seconds = Math.floor((diff % 60000) / 1000);

        timerLabel.textContent =
            `${minutes.toString().padStart(2, "0")}:${seconds
                .toString().padStart(2, "0")}`;

    }, 1000);
}

function showMainUI() {
    document.getElementById("timerScreen").style.display = "none";
    document.getElementById("mainFormContainer").style.display = "block";
}
</script>
