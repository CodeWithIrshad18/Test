using System.Security.Claims;

[Authorize]
public IActionResult GeoFencing()
{
    var userId = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
    var userName = User.Identity?.Name;

    if (string.IsNullOrEmpty(userId))
    {
        return RedirectToAction("Login", "User");
    }

    string connectionString = GetRFIDConnectionString();
    DateTime? dischargeDate;

    using (var connection = new SqlConnection(connectionString))
    {
        dischargeDate = connection.QuerySingleOrDefault<DateTime?>(
            @"SELECT ema_Disch_dt 
              FROM SAPHRDB.dbo.T_EMPL_ALL 
              WHERE ema_Perno = @Pno",
            new { Pno = userId }
        );
    }

    if (dischargeDate.HasValue && dischargeDate.Value.Date <= DateTime.Today)
    {
        return RedirectToAction("Login", "User");
    }

    ViewBag.UserId = userId;
    ViewBag.UserName = userName;

    var data = GetLocations();

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

    return View();
}

 
 
 
 [Authorize]
 public IActionResult GeoFencing()
 {
     var userId = HttpContext.Request.Cookies["Session"];
     var userName = HttpContext.Request.Cookies["UserName"];

     if (string.IsNullOrEmpty(userId) || string.IsNullOrEmpty(userName))
     {
         return RedirectToAction("Login", "User");
     }
     string connectionString = GetRFIDConnectionString();

     DateTime? dischargeDate;

     using (var connection = new SqlConnection(connectionString))
     {
         dischargeDate = connection.QuerySingleOrDefault<DateTime?>(
             @"SELECT ema_Disch_dt 
       FROM SAPHRDB.dbo.T_EMPL_ALL 
       WHERE ema_Perno = @Pno",
             new { Pno = userId }
         );
     }

     if (dischargeDate.HasValue && dischargeDate.Value.Date <= DateTime.Today)
     {

         return RedirectToAction("Login","User"); 
     }




     ViewBag.UserId = userId;
     ViewBag.UserName = userName;

     var data = GetLocations();

     var pno = userId;
     var currentDate = DateTime.Now.ToString("yyyy-MM-dd");

    

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
