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

    // Punch Count (IN/OUT logic)
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

    // Fetch Latest Punch Time
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

      string query = @"
  SELECT COUNT(*) 
  FROM T_TRBDGDAT_EARS 
  WHERE TRBDGDA_BD_PNO = @Pno 
  AND TRBDGDA_BD_DATE = @CurrentDate";


      int punchCount = 0;

      using (var connection = new SqlConnection(connectionString))
      {
          punchCount = connection.QuerySingleOrDefault<int>(query, new { Pno = pno, CurrentDate = currentDate });
      }

      int mod = punchCount % 2;
      ViewBag.InOut = mod == 0 ? "I" : "O";



      return View();
  }

query to fetch latest time like this 16:03 in my table 

select top 1 * from T_TRPUNCHDATA_EARS where  PDE_PSRNO='151514' and PDE_PUNCHDATE = '2025-11-18' order by PDE_PUNCHTIME desc

i want my controller to fetch this 
