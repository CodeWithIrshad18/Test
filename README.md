public IActionResult AttendanceReport()
{
    // Get current logged-in employee number from cookies
    string PersonalNo = HttpContext.Request.Cookies["Session"];

    if (string.IsNullOrEmpty(PersonalNo))
    {
        return RedirectToAction("Login", "User");
    }

    IEnumerable<AttendanceReportModel> reportData;

    using (var connection = new SqlConnection(GetRFIDConnectionString()))
    {
        string query = @"
WITH dateseries AS (
    SELECT 
        DATEADD(DAY, number, DATEFROMPARTS(YEAR(GETDATE()), MONTH(GETDATE()), 1)) AS punchdate 
    FROM master.dbo.spt_values 
    WHERE type = 'p' 
        AND DATEADD(DAY, number, DATEFROMPARTS(YEAR(GETDATE()), MONTH(GETDATE()), 1)) 
            <= EOMONTH(GETDATE())
),
FilteredPunches AS (
    SELECT 
        t.TRBDGDA_BD_PNO, 
        t.TRBDGDA_BD_DATE, 
        CONVERT(TIME, DATEADD(MINUTE, t.TRBDGDA_BD_TIME, 0)) AS PunchTime,
        ROW_NUMBER() OVER (
            PARTITION BY t.TRBDGDA_BD_PNO, t.TRBDGDA_BD_DATE 
            ORDER BY t.TRBDGDA_BD_TIME
        ) AS rn
    FROM TSUISLRFIDDB.dbo.T_TRBDGDAT_EARS t
    WHERE t.TRBDGDA_BD_PNO = @PsrNo 
      AND (t.TRBDGDA_BD_ENTRYUID = 'MOBILE' OR t.TRBDGDA_BD_ENTRYUID = 'COA')
),
ValidPunches AS (
    SELECT 
        fp.*, 
        MIN(PunchTime) OVER (PARTITION BY TRBDGDA_BD_PNO, TRBDGDA_BD_DATE) AS FirstPunch, 
        DATEDIFF(MINUTE, 
            MIN(PunchTime) OVER (PARTITION BY TRBDGDA_BD_PNO, TRBDGDA_BD_DATE), 
            PunchTime) AS MinDiff 
    FROM FilteredPunches fp
),
FilteredValidPunches AS (
    SELECT * 
    FROM ValidPunches 
    WHERE MinDiff >= 5 OR rn = 1
),
AllPunches AS (
    SELECT 
        TRBDGDA_BD_DATE,
        COUNT(*) AS AllPunchCount
    FROM TSUISLRFIDDB.dbo.T_TRBDGDAT_EARS
    WHERE TRBDGDA_BD_PNO = @PsrNo 
      AND (TRBDGDA_BD_ENTRYUID = 'MOBILE' OR TRBDGDA_BD_ENTRYUID = 'COA')
    GROUP BY TRBDGDA_BD_DATE
)
SELECT 
    FORMAT(ds.punchdate, 'dd-MM-yyyy') AS TRBDGDA_BD_DATE,
    FORMAT(ISNULL(MIN(fvp.PunchTime), '00:00:00'), 'HH:mm:ss') AS PunchInTime,
    FORMAT(
        ISNULL(
            CASE 
                WHEN COUNT(fvp.PunchTime) > 1 THEN MAX(fvp.PunchTime)
                ELSE NULL 
            END, 
            '00:00:00'
        ), 'HH:mm:ss'
    ) AS PunchOutTime,
    ISNULL(ap.AllPunchCount, 0) AS SumOfPunching
FROM dateseries ds
LEFT JOIN FilteredValidPunches fvp 
    ON ds.punchdate = fvp.TRBDGDA_BD_DATE
LEFT JOIN AllPunches ap 
    ON ds.punchdate = ap.TRBDGDA_BD_DATE
GROUP BY ds.punchdate, ap.AllPunchCount
ORDER BY ds.punchdate ASC;
";

        reportData = connection.Query<AttendanceReportModel>(query, new { PsrNo = PersonalNo });
    }

    // Add serial numbers
    int sl = 1;
    foreach (var item in reportData)
        item.SlNo = sl++;

    // Pass month-year for display (e.g. November 2025)
    var monthYear = DateTime.Now.ToString("MMMM yyyy", CultureInfo.InvariantCulture);
    ViewBag.MonthYear = monthYear;

    return View(reportData);
}

@model IEnumerable<YourNamespace.Models.AttendanceReportModel>

<h3 class="text-center">Attendance Report - @ViewBag.MonthYear</h3>

<table class="table table-bordered text-center">
    <thead class="table-dark">
        <tr>
            <th>Sl No</th>
            <th>Date</th>
            <th>Punch In</th>
            <th>Punch Out</th>
            <th>Total Punches</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var item in Model)
        {
            <tr>
                <td>@item.SlNo</td>
                <td>@item.TRBDGDA_BD_DATE</td>
                <td>@item.PunchInTime</td>
                <td>@item.PunchOutTime</td>
                <td>@item.SumOfPunching</td>
            </tr>
        }
    </tbody>
</table>




using Dapper;
using Microsoft.AspNetCore.Mvc;
using System.Data.SqlClient;
using System.Globalization;

public class GeoController : Controller
{
    private readonly IConfiguration configuration;

    public GeoController(IConfiguration config)
    {
        configuration = config;
    }

    private string GetRFIDConnectionString()
    {
        return this.configuration.GetConnectionString("RFID");
    }

    public IActionResult AttendanceReport()
    {
        // ✅ Get logged-in user’s personal number from cookie
        string personalNo = HttpContext.Request.Cookies["Session"];

        if (string.IsNullOrEmpty(personalNo))
        {
            // If no cookie, redirect to login
            return RedirectToAction("Login", "User");
        }

        IEnumerable<AttendanceReportModel> reportData;

        using (var connection = new SqlConnection(GetRFIDConnectionString()))
        {
            string query = @"
WITH dateseries AS (
    SELECT 
        DATEADD(DAY, number, DATEFROMPARTS(YEAR(GETDATE()), MONTH(GETDATE()), 1)) AS punchdate 
    FROM master.dbo.spt_values 
    WHERE type = 'p' 
        AND DATEADD(DAY, number, DATEFROMPARTS(YEAR(GETDATE()), MONTH(GETDATE()), 1)) 
            <= EOMONTH(GETDATE())
),
FilteredPunches AS (
    SELECT 
        t.TRBDGDA_BD_PNO, 
        t.TRBDGDA_BD_DATE, 
        CONVERT(TIME, DATEADD(MINUTE, t.TRBDGDA_BD_TIME, 0)) AS PunchTime,
        ROW_NUMBER() OVER (
            PARTITION BY t.TRBDGDA_BD_PNO, t.TRBDGDA_BD_DATE 
            ORDER BY t.TRBDGDA_BD_TIME
        ) AS rn
    FROM TSUISLRFIDDB.dbo.T_TRBDGDAT_EARS t
    WHERE t.TRBDGDA_BD_PNO = @PsrNo 
      AND (TRBDGDA_BD_ENTRYUID ='MOBILE' OR TRBDGDA_BD_ENTRYUID ='COA')
),
ValidPunches AS (
    SELECT 
        fp.*, 
        MIN(PunchTime) OVER (PARTITION BY TRBDGDA_BD_PNO, TRBDGDA_BD_DATE) AS FirstPunch, 
        DATEDIFF(MINUTE, 
            MIN(PunchTime) OVER (PARTITION BY TRBDGDA_BD_PNO, TRBDGDA_BD_DATE), 
            PunchTime) AS MinDiff 
    FROM FilteredPunches fp
),
FilteredValidPunches AS (
    SELECT * 
    FROM ValidPunches 
    WHERE MinDiff >= 5 OR rn = 1
),
AllPunches AS (
    SELECT 
        TRBDGDA_BD_DATE,
        COUNT(*) AS AllPunchCount
    FROM TSUISLRFIDDB.dbo.T_TRBDGDAT_EARS
    WHERE TRBDGDA_BD_PNO = @PsrNo 
      AND (TRBDGDA_BD_ENTRYUID ='MOBILE' OR TRBDGDA_BD_ENTRYUID ='COA')
    GROUP BY TRBDGDA_BD_DATE
)
SELECT 
    FORMAT(ds.punchdate, 'dd-MM-yyyy') AS TRBDGDA_BD_DATE,
    ISNULL(MIN(fvp.PunchTime), '00:00:00') AS PunchInTime,
    ISNULL(
        CASE 
            WHEN COUNT(fvp.PunchTime) > 1 THEN MAX(fvp.PunchTime)
            ELSE NULL 
        END, 
        '00:00:00'
    ) AS PunchOutTime,
    ISNULL(ap.AllPunchCount, 0) AS SumOfPunching
FROM dateseries ds
LEFT JOIN FilteredValidPunches fvp 
    ON ds.punchdate = fvp.TRBDGDA_BD_DATE
LEFT JOIN AllPunches ap 
    ON ds.punchdate = ap.TRBDGDA_BD_DATE
GROUP BY ds.punchdate, ap.AllPunchCount
ORDER BY ds.punchdate ASC;";

            // ✅ Use PersonalNo from cookie instead of psrNo param
            reportData = connection.Query<AttendanceReportModel>(query, new { PsrNo = personalNo });
        }

        // Add serial numbers
        int sl = 1;
        foreach (var item in reportData)
            item.SlNo = sl++;

        // Month-Year Header
        ViewBag.MonthYear = DateTime.Now.ToString("MMMM yyyy", CultureInfo.InvariantCulture);
        ViewBag.PersonalNo = personalNo;

        return View(reportData);
    }
}

@model IEnumerable<AttendanceReportModel>

@{
    var monthYear = ViewBag.MonthYear ?? "";
    var personalNo = ViewBag.PersonalNo ?? "";
}

<h2 style="text-align:center; margin-bottom:5px;">Attendance Report</h2>
<h4 style="text-align:center; color:#333; margin-top:0;">
    @monthYear
</h4>
<h5 style="text-align:center; color:#666; margin-bottom:20px;">
    Employee No: <b>@personalNo</b>
</h5>

<table border="1" style="width:100%; border-collapse:collapse; text-align:center;">
    <thead style="background-color:#1b144b; color:white;">
        <tr>
            <th>Sl.No</th>
            <th>PUNCH DATE</th>
            <th>IN</th>
            <th>OUT</th>
            <th>Punch Count</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var item in Model)
        {
            <tr>
                <td>@item.SlNo</td>
                <td>@item.TRBDGDA_BD_DATE</td>
                <td><b>@item.PunchInTime.Substring(0, 8)</b></td>
                <td><b>@item.PunchOutTime.Substring(0, 8)</b></td>
                <td>@item.SumOfPunching</td>
            </tr>
        }
    </tbody>
</table>

 
 
 
 

private string GetRFIDConnectionString()
{
    return this.configuration.GetConnectionString("RFID");
}

 string PersonalNo = HttpContext.Request.Cookies["Session"];

 if (string.IsNullOrEmpty(PersonalNo))
 {
     return RedirectToAction("Login", "User");
 }


        public IActionResult AttendanceReport(string psrNo)
        {
            IEnumerable<AttendanceReportModel> reportData;

            using (var connection = new SqlConnection(GetRFIDConnectionString()))
            {
                string query = @"-- your same query here
WITH dateseries AS (
    SELECT 
        DATEADD(DAY, number, DATEFROMPARTS(YEAR(GETDATE()), MONTH(GETDATE()), 1)) AS punchdate 
    FROM master.dbo.spt_values 
    WHERE type = 'p' 
        AND DATEADD(DAY, number, DATEFROMPARTS(YEAR(GETDATE()), MONTH(GETDATE()), 1)) 
            <= EOMONTH(GETDATE())
),
FilteredPunches AS (
    SELECT 
        t.TRBDGDA_BD_PNO, 
        t.TRBDGDA_BD_DATE, 
        CONVERT(TIME, DATEADD(MINUTE, t.TRBDGDA_BD_TIME, 0)) AS PunchTime,
        ROW_NUMBER() OVER (
            PARTITION BY t.TRBDGDA_BD_PNO, t.TRBDGDA_BD_DATE 
            ORDER BY t.TRBDGDA_BD_TIME
        ) AS rn
    FROM TSUISLRFIDDB.dbo.T_TRBDGDAT_EARS t
    WHERE t.TRBDGDA_BD_PNO = @PsrNo and (TRBDGDA_BD_ENTRYUID ='MOBILE' or TRBDGDA_BD_ENTRYUID ='COA')
),
ValidPunches AS (
    SELECT 
        fp.*, 
        MIN(PunchTime) OVER (PARTITION BY TRBDGDA_BD_PNO, TRBDGDA_BD_DATE) AS FirstPunch, 
        DATEDIFF(MINUTE, 
            MIN(PunchTime) OVER (PARTITION BY TRBDGDA_BD_PNO, TRBDGDA_BD_DATE), 
            PunchTime) AS MinDiff 
    FROM FilteredPunches fp
),
FilteredValidPunches AS (
    SELECT * 
    FROM ValidPunches 
    WHERE MinDiff >= 5 OR rn = 1
),
AllPunches AS (
    SELECT 
        TRBDGDA_BD_DATE,
        COUNT(*) AS AllPunchCount
    FROM TSUISLRFIDDB.dbo.T_TRBDGDAT_EARS
    WHERE TRBDGDA_BD_PNO = @PsrNo and (TRBDGDA_BD_ENTRYUID ='MOBILE' or TRBDGDA_BD_ENTRYUID ='COA')
    GROUP BY TRBDGDA_BD_DATE
)
SELECT 
    FORMAT(ds.punchdate, 'dd-MM-yyyy') AS TRBDGDA_BD_DATE,
    ISNULL(MIN(fvp.PunchTime), '00:00:00') AS PunchInTime,
    ISNULL(
        CASE 
            WHEN COUNT(fvp.PunchTime) > 1 THEN MAX(fvp.PunchTime)
            ELSE NULL 
        END, 
        '00:00:00'
    ) AS PunchOutTime,
    ISNULL(ap.AllPunchCount, 0) AS SumOfPunching
FROM dateseries ds
LEFT JOIN FilteredValidPunches fvp 
    ON ds.punchdate = fvp.TRBDGDA_BD_DATE
LEFT JOIN AllPunches ap 
    ON ds.punchdate = ap.TRBDGDA_BD_DATE
GROUP BY ds.punchdate, ap.AllPunchCount
ORDER BY ds.punchdate ASC;";

                reportData = connection.Query<AttendanceReportModel>(query, new { PsrNo = psrNo });
            }

            int sl = 1;
            foreach (var item in reportData)
                item.SlNo = sl++;

            var monthYear = DateTime.Now.ToString("MMMM yyyy", CultureInfo.InvariantCulture);
            ViewBag.MonthYear = monthYear;

            return View(reportData);
        }
