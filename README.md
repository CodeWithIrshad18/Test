using Dapper;
using Microsoft.AspNetCore.Mvc;
using System.Data.SqlClient;
using System.Globalization;

public class GeoController : Controller
{
    private readonly string _connectionString = "<your_connection_string>";

    public IActionResult AttendanceReport(string psrNo)
    {
        IEnumerable<AttendanceReportModel> reportData;

        using (var connection = new SqlConnection(_connectionString))
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

        // ✅ Set dynamic month & year header
        var monthYear = DateTime.Now.ToString("MMMM yyyy", CultureInfo.InvariantCulture);
        ViewBag.MonthYear = monthYear;

        return View(reportData);
    }
}


@model IEnumerable<AttendanceReportModel>

@{
    var monthYear = ViewBag.MonthYear ?? "";
}

<h2 style="text-align:center; margin-bottom:5px;">Attendance Report</h2>
<h4 style="text-align:center; color:#333; margin-top:0;">@monthYear</h4>

<table border="1" style="width:100%; border-collapse:collapse; text-align:center; margin-top:20px;">
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

public class AttendanceReportModel
{
    public string TRBDGDA_BD_DATE { get; set; }
    public string PunchInTime { get; set; }
    public string PunchOutTime { get; set; }
    public int SumOfPunching { get; set; }

    // Optional computed property for serial number in the view
    public int SlNo { get; set; }
}





TRBDGDA_BD_DATE	PunchInTime	PunchOutTime	SumOfPunching
01-11-2025	00:00:00.0000000	00:00:00.0000000	0
02-11-2025	00:00:00.0000000	00:00:00.0000000	0
03-11-2025	09:18:00.0000000	19:27:00.0000000	3
04-11-2025	09:30:00.0000000	19:07:00.0000000	2
05-11-2025	09:46:00.0000000	19:19:00.0000000	2
06-11-2025	09:12:00.0000000	18:51:00.0000000	2
07-11-2025	09:23:00.0000000	19:05:00.0000000	2
08-11-2025	00:00:00.0000000	00:00:00.0000000	0
09-11-2025	00:00:00.0000000	00:00:00.0000000	0
10-11-2025	09:31:00.0000000	00:00:00.0000000	1
11-11-2025	00:00:00.0000000	00:00:00.0000000	0
12-11-2025	00:00:00.0000000	00:00:00.0000000	0
13-11-2025	00:00:00.0000000	00:00:00.0000000	0
14-11-2025	00:00:00.0000000	00:00:00.0000000	0
15-11-2025	00:00:00.0000000	00:00:00.0000000	0
16-11-2025	00:00:00.0000000	00:00:00.0000000	0
17-11-2025	00:00:00.0000000	00:00:00.0000000	0
18-11-2025	00:00:00.0000000	00:00:00.0000000	0
19-11-2025	00:00:00.0000000	00:00:00.0000000	0
20-11-2025	00:00:00.0000000	00:00:00.0000000	0
21-11-2025	00:00:00.0000000	00:00:00.0000000	0
22-11-2025	00:00:00.0000000	00:00:00.0000000	0
23-11-2025	00:00:00.0000000	00:00:00.0000000	0
24-11-2025	00:00:00.0000000	00:00:00.0000000	0
25-11-2025	00:00:00.0000000	00:00:00.0000000	0
26-11-2025	00:00:00.0000000	00:00:00.0000000	0
27-11-2025	00:00:00.0000000	00:00:00.0000000	0
28-11-2025	00:00:00.0000000	00:00:00.0000000	0
29-11-2025	00:00:00.0000000	00:00:00.0000000	0
30-11-2025	00:00:00.0000000	00:00:00.0000000	0
