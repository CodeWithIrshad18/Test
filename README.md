public IActionResult GeoFencing()
{
    var userId = HttpContext.Request.Cookies["Session"];
    var userName = HttpContext.Request.Cookies["UserName"];

    if (string.IsNullOrEmpty(userId) || string.IsNullOrEmpty(userName))
    {
        return RedirectToAction("Login", "User");
    }

    string connectionString = GetRFIDConnectionString();

    // 🔴 CHECK DISCHARGE DATE
    DateTime? dischargeDate;

    using (var connection = new SqlConnection(connectionString))
    {
        dischargeDate = connection.QuerySingleOrDefault<DateTime?>(
            @"SELECT ema_Disch_dt 
              FROM SAPHRDB.dbo.T_EMPL_ALL 
              WHERE ema_Pno = @Pno",
            new { Pno = userId }
        );
    }

    // If employee is discharged
    if (dischargeDate.HasValue && dischargeDate.Value.Date <= DateTime.Today)
    {
        return View("AccessDenied"); // or RedirectToAction(...)
    }

    // ✅ Normal flow continues
    ViewBag.UserId = userId;
    ViewBag.UserName = userName;

    var currentDate = DateTime.Now.ToString("yyyy-MM-dd");

    string countQuery = @"
        SELECT COUNT(*) 
        FROM T_TRBDGDAT_EARS 
        WHERE TRBDGDA_BD_PNO = @Pno 
        AND TRBDGDA_BD_DATE = @CurrentDate";

    int punchCount;

    using (var connection = new SqlConnection(connectionString))
    {
        punchCount = connection.QuerySingleOrDefault<int>(
            countQuery,
            new { Pno = userId, CurrentDate = currentDate }
        );
    }

    ViewBag.InOut = punchCount % 2 == 0 ? "I" : "O";

    string latestPunchQuery = @"
        SELECT TOP 1 PDE_PUNCHTIME 
        FROM T_TRPUNCHDATA_EARS 
        WHERE PDE_PSRNO = @Pno 
        AND PDE_PUNCHDATE = @CurrentDate
        ORDER BY PDE_PUNCHTIME DESC";

    using (var connection = new SqlConnection(connectionString))
    {
        ViewBag.LatestPunchTime = connection.QuerySingleOrDefault<string>(
            latestPunchQuery,
            new { Pno = userId, CurrentDate = currentDate }
        ) ?? "";
    }

    return View();
}

 
 
 
 
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

     ViewBag.LatestPunchTime = latestPunchTime ?? ""; ;

     return View();
 }

select * from SAPHRDB.dbo.T_EMPL_ALL where ema_Disch_dt is not null
