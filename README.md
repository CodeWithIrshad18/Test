public class AttendanceReportModel
{
    public string PunchDate { get; set; }
    public string PunchInTime { get; set; }
    public string PunchOutTime { get; set; }
    public int PunchCount { get; set; }
}

public IActionResult AttendanceReport()
{
    var reports = new List<AttendanceReportModel>();

    string connectionString = "YourConnectionStringHere";
    string query = @"WITH dateseries AS (
                        SELECT DATEADD(DAY, number, DATEFROMPARTS(YEAR(GETDATE()), MONTH(GETDATE()), 1)) AS punchdate 
                        FROM master.dbo.spt_values 
                        WHERE type = 'p' 
                            AND DATEADD(DAY, number, DATEFROMPARTS(YEAR(GETDATE()), MONTH(GETDATE()), 1)) <= EOMONTH(GETDATE())
                    ),
                    FilteredPunches AS (
                        SELECT t.TRBDGDA_BD_PNO, 
                               t.TRBDGDA_BD_DATE, 
                               CONVERT(TIME, DATEADD(MINUTE, t.TRBDGDA_BD_TIME, 0)) AS PunchTime,
                               ROW_NUMBER() OVER (PARTITION BY t.TRBDGDA_BD_PNO, t.TRBDGDA_BD_DATE ORDER BY t.TRBDGDA_BD_TIME) AS rn
                        FROM TSUISLRFIDDB.dbo.T_TRBDGDAT_EARS t
                        WHERE t.TRBDGDA_BD_PNO = '151514' 
                          AND (TRBDGDA_BD_ENTRYUID ='MOBILE' OR TRBDGDA_BD_ENTRYUID ='COA')
                    ),
                    ValidPunches AS (
                        SELECT fp.*, 
                               MIN(PunchTime) OVER (PARTITION BY TRBDGDA_BD_PNO, TRBDGDA_BD_DATE) AS FirstPunch, 
                               DATEDIFF(MINUTE, MIN(PunchTime) OVER (PARTITION BY TRBDGDA_BD_PNO, TRBDGDA_BD_DATE), PunchTime) AS MinDiff 
                        FROM FilteredPunches fp
                    ),
                    FilteredValidPunches AS (
                        SELECT * FROM ValidPunches WHERE MinDiff >= 5 OR rn = 1
                    ),
                    AllPunches AS (
                        SELECT TRBDGDA_BD_DATE, COUNT(*) AS AllPunchCount
                        FROM TSUISLRFIDDB.dbo.T_TRBDGDAT_EARS
                        WHERE TRBDGDA_BD_PNO = '151514' 
                          AND (TRBDGDA_BD_ENTRYUID ='MOBILE' OR TRBDGDA_BD_ENTRYUID ='COA')
                        GROUP BY TRBDGDA_BD_DATE
                    )
                    SELECT FORMAT(ds.punchdate, 'dd-MM-yyyy') AS TRBDGDA_BD_DATE,
                           ISNULL(MIN(fvp.PunchTime), '00:00:00') AS PunchInTime,
                           ISNULL(CASE WHEN COUNT(fvp.PunchTime) > 1 THEN MAX(fvp.PunchTime) ELSE NULL END, '00:00:00') AS PunchOutTime,
                           ISNULL(ap.AllPunchCount, 0) AS SumOfPunching
                    FROM dateseries ds
                    LEFT JOIN FilteredValidPunches fvp ON ds.punchdate = fvp.TRBDGDA_BD_DATE
                    LEFT JOIN AllPunches ap ON ds.punchdate = ap.TRBDGDA_BD_DATE
                    GROUP BY ds.punchdate, ap.AllPunchCount
                    ORDER BY ds.punchdate ASC";

    using (SqlConnection con = new SqlConnection(connectionString))
    {
        SqlCommand cmd = new SqlCommand(query, con);
        con.Open();
        SqlDataReader dr = cmd.ExecuteReader();

        while (dr.Read())
        {
            reports.Add(new AttendanceReportModel
            {
                PunchDate = dr["TRBDGDA_BD_DATE"].ToString(),
                PunchInTime = dr["PunchInTime"].ToString(),
                PunchOutTime = dr["PunchOutTime"].ToString(),
                PunchCount = Convert.ToInt32(dr["SumOfPunching"])
            });
        }
        con.Close();
    }

    return View(reports);
}

@model IEnumerable<YourNamespace.Models.AttendanceReportModel>

<h2>Attendance Report</h2>

<table class="table table-bordered">
    <thead>
        <tr>
            <th>Sl.No</th>
            <th>Punch Date</th>
            <th>In</th>
            <th>Out</th>
            <th>Punch Count</th>
        </tr>
    </thead>
    <tbody>
        @if (Model != null)
        {
            int sl = 1;
            foreach (var item in Model)
            {
                <tr>
                    <td>@sl</td>
                    <td>@item.PunchDate</td>
                    <td>@item.PunchInTime</td>
                    <td>@item.PunchOutTime</td>
                    <td>@item.PunchCount</td>
                </tr>
                sl++;
            }
        }
    </tbody>
</table>




this is my query 

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
    WHERE t.TRBDGDA_BD_PNO = '151514' and (TRBDGDA_BD_ENTRYUID ='MOBILE' or TRBDGDA_BD_ENTRYUID ='COA')
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
    WHERE TRBDGDA_BD_PNO = '151514' and (TRBDGDA_BD_ENTRYUID ='MOBILE' or TRBDGDA_BD_ENTRYUID ='COA')
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
ORDER BY ds.punchdate ASC

this is my controller method 

public IActionResult AttendanceReport()
{
    return View();
}

