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
    CONVERT(VARCHAR(8), ISNULL(MIN(fvp.PunchTime), CAST('00:00:00' AS TIME)), 108) AS PunchInTime,
    CONVERT(VARCHAR(8), ISNULL(
        CASE 
            WHEN COUNT(fvp.PunchTime) > 1 THEN MAX(fvp.PunchTime)
            ELSE NULL 
        END, 
        CAST('00:00:00' AS TIME)
    ), 108) AS PunchOutTime,
    ISNULL(ap.AllPunchCount, 0) AS SumOfPunching
FROM dateseries ds
LEFT JOIN FilteredValidPunches fvp 
    ON ds.punchdate = fvp.TRBDGDA_BD_DATE
LEFT JOIN AllPunches ap 
    ON ds.punchdate = ap.TRBDGDA_BD_DATE
GROUP BY ds.punchdate, ap.AllPunchCount
ORDER BY ds.punchdate ASC;





@{
    Layout = null;
}

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>Attendance Report</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">

    <style>
        body {
            background-color: #f9fafb;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            padding: 10px;
        }

        .report-card {
            background-color: #ffffff;
            border-radius: 8px;
            box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
            padding: 20px;
            max-width: 900px;
            margin: auto;
        }

        .report-header {
            text-align: center;
            margin-bottom: 15px;
        }

        .report-header h4 {
            font-weight: 600;
            color: #1b144b;
        }

        .report-header h6 {
            color: #555;
            margin-top: 3px;
        }

        .table-responsive {
            overflow-x: auto;
            -webkit-overflow-scrolling: touch;
        }

        table {
            border-collapse: collapse;
            width: 100%;
            white-space: nowrap; /* Prevents text from wrapping */
        }

        thead {
            background-color: #1b144b;
            color: #ffffff;
        }

        th, td {
            padding: 6px 8px; /* reduced padding */
            text-align: center;
            border: 1px solid #ddd;
            font-size: 14px;
        }

        tbody tr:nth-child(even) {
            background-color: #f8f9fa;
        }

        tbody tr:hover {
            background-color: #eef4ff;
        }

        .footer-note {
            text-align: center;
            color: #777;
            font-size: 12px;
            margin-top: 10px;
        }

        /* ✅ Mobile adjustments */
        @media (max-width: 600px) {
            body {
                padding: 5px;
            }

            th, td {
                padding: 4px 6px;
                font-size: 13px;
            }

            .report-card {
                padding: 12px;
                box-shadow: none;
            }

            .report-header h4 {
                font-size: 16px;
            }

            .report-header h5, .report-header h6 {
                font-size: 13px;
            }
        }
    </style>
</head>
<body>
    <div class="report-card">
        <div class="report-header">
            <h4>Attendance Report</h4>
            <h5>@monthYear</h5>
            <h6>Employee No: <b>@personalNo</b></h6>
        </div>

        <div class="table-responsive">
            <table>
                <thead>
                    <tr>
                        <th>Sl. No</th>
                        <th>Date</th>
                        <th>Punch In</th>
                        <th>Punch Out</th>
                        <th>Count</th>
                    </tr>
                </thead>
                <tbody>
                    @if (Model != null && Model.Any())
                    {
                        foreach (var item in Model)
                        {
                            <tr>
                                <td>@item.SlNo</td>
                                <td>@item.TRBDGDA_BD_DATE</td>
                                <td><b>@item.PunchInTime</b></td>
                                <td><b>@item.PunchOutTime</b></td>
                                <td>@item.SumOfPunching</td>
                            </tr>
                        }
                    }
                    else
                    {
                        <tr>
                            <td colspan="5">No data available for this month.</td>
                        </tr>
                    }
                </tbody>
            </table>
        </div>

        <div class="footer-note">
            Generated on @DateTime.Now.ToString("dd MMM yyyy hh:mm tt")
        </div>
    </div>
</body>
</html>




@{
    Layout = null;
}

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>Attendance Report</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">

    <style>
        body {
            background-color: #f9fafb;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            padding: 20px;
        }

        .report-card {
            background-color: #ffffff;
            border-radius: 8px;
            box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
            padding: 25px;
            max-width: 900px;
            margin: auto;
        }

        .report-header {
            text-align: center;
            margin-bottom: 20px;
        }

        .report-header h4 {
            font-weight: 600;
            color: #1b144b;
        }

        .report-header h5 {
            color: #555;
            margin-bottom: 0;
        }

        table {
            border-collapse: collapse;
            width: 100%;
            margin-top: 10px;
        }

        thead {
            background-color: #1b144b;
            color: #ffffff;
        }

        th, td {
            padding: 10px;
            text-align: center;
            border: 1px solid #ddd;
        }

        tbody tr:nth-child(even) {
            background-color: #f3f3f3;
        }

        tbody tr:hover {
            background-color: #e8f0fe;
        }

        .footer-note {
            text-align: center;
            color: #777;
            font-size: 13px;
            margin-top: 15px;
        }

        @media print {
            body {
                background: white;
            }
            .report-card {
                box-shadow: none;
                border: none;
            }
        }
    </style>
</head>
<body>
    <div class="report-card">
        <div class="report-header">
            <h4>Attendance Report</h4>
            <h5>@monthYear</h5>
            <h6>Employee No: <b>@personalNo</b></h6>
        </div>

        <table>
            <thead>
                <tr>
                    <th>Sl. No</th>
                    <th>Punch Date</th>
                    <th>Punch In</th>
                    <th>Punch Out</th>
                    <th>Punch Count</th>
                </tr>
            </thead>
            <tbody>
                @if (Model != null && Model.Any())
                {
                    foreach (var item in Model)
                    {
                        <tr>
                            <td>@item.SlNo</td>
                            <td>@item.TRBDGDA_BD_DATE</td>
                            <td><b>@item.PunchInTime</b></td>
                            <td><b>@item.PunchOutTime</b></td>
                            <td>@item.SumOfPunching</td>
                        </tr>
                    }
                }
                else
                {
                    <tr>
                        <td colspan="5">No data available for this month.</td>
                    </tr>
                }
            </tbody>
        </table>

        <div class="footer-note">
            Generated on @DateTime.Now.ToString("dd MMM yyyy hh:mm tt")
        </div>
    </div>
</body>
</html>






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
    CONVERT(VARCHAR(8), ISNULL(MIN(fvp.PunchTime), CAST('00:00:00' AS TIME)), 108) AS PunchInTime,
    CONVERT(VARCHAR(8), ISNULL(
        CASE 
            WHEN COUNT(fvp.PunchTime) > 1 THEN MAX(fvp.PunchTime)
            ELSE NULL 
        END, 
        CAST('00:00:00' AS TIME)
    ), 108) AS PunchOutTime,
    ISNULL(ap.AllPunchCount, 0) AS SumOfPunching
FROM dateseries ds
LEFT JOIN FilteredValidPunches fvp 
    ON ds.punchdate = fvp.TRBDGDA_BD_DATE
LEFT JOIN AllPunches ap 
    ON ds.punchdate = ap.TRBDGDA_BD_DATE
GROUP BY ds.punchdate, ap.AllPunchCount
ORDER BY ds.punchdate ASC;





SELECT 
    FORMAT(ds.punchdate, 'dd-MM-yyyy') AS TRBDGDA_BD_DATE,
    FORMAT(ISNULL(MIN(fvp.PunchTime), CAST('00:00:00' AS TIME)), 'HH:mm:ss') AS PunchInTime,
    FORMAT(
        ISNULL(
            CASE 
                WHEN COUNT(fvp.PunchTime) > 1 THEN MAX(fvp.PunchTime)
                ELSE NULL 
            END, 
            CAST('00:00:00' AS TIME)
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




GFAS.Models.AttendanceReportModel,01-

11-2025,"","",0,1
GFAS.Models.AttendanceReportModel,02-11-2025,"","",0,2
GFAS.Models.AttendanceReportModel,03-11-2025,"","",3,3
GFAS.Models.AttendanceReportModel,04-11-2025,"","",2,4
GFAS.Models.AttendanceReportModel,05-11-2025,"","",2,5
GFAS.Models.AttendanceReportModel,06-11-2025,"","",2,6
GFAS.Models.AttendanceReportModel,07-11-2025,"","",2,7
GFAS.Models.AttendanceReportModel,08-11-2025,"","",0,8
GFAS.Models.AttendanceReportModel,09-11-2025,"","",0,9
GFAS.Models.AttendanceReportModel,10-11-2025,"","",1,10
GFAS.Models.AttendanceReportModel,11-11-2025,"","",0,11
GFAS.Models.AttendanceReportModel,12-11-2025,"","",0,12
GFAS.Models.AttendanceReportModel,13-11-2025,"","",0,13
GFAS.Models.AttendanceReportModel,14-11-2025,"","",0,14
GFAS.Models.AttendanceReportModel,15-11-2025,"","",0,15
GFAS.Models.AttendanceReportModel,16-11-2025,"","",0,16
GFAS.Models.AttendanceReportModel,17-11-2025,"","",0,17
GFAS.Models.AttendanceReportModel,18-11-2025,"","",0,18
GFAS.Models.AttendanceReportModel,19-11-2025,"","",0,19
GFAS.Models.AttendanceReportModel,20-11-2025,"","",0,20
GFAS.Models.AttendanceReportModel,21-11-2025,"","",0,21
GFAS.Models.AttendanceReportModel,22-11-2025,"","",0,22
GFAS.Models.AttendanceReportModel,23-11-2025,"","",0,23
GFAS.Models.AttendanceReportModel,24-11-2025,"","",0,24
GFAS.Models.AttendanceReportModel,25-11-2025,"","",0,25
GFAS.Models.AttendanceReportModel,26-11-2025,"","",0,26
GFAS.Models.AttendanceReportModel,27-11-2025,"","",0,27
GFAS.Models.AttendanceReportModel,28-11-2025,"","",0,28
GFAS.Models.AttendanceReportModel,29-11-2025,"","",0,29
GFAS.Models.AttendanceReportModel,30-11-2025,"","",0,30



 public class AttendanceReportModel
 {
     
         public string? TRBDGDA_BD_DATE { get; set; }
         public string?punchintime { get; set; }
         public string?Punchouttime { get; set; }
         public int SumOfPunching { get; set; }

         public int SlNo { get; set; }
     
 }

<div class="container">
  <h4 style="text-align:center; margin-bottom:5px;">Attendance Report</h2>
<h5 style="text-align:center; color:#333; margin-top:0;">
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
                <td><b>@item.punchintime</b></td>
                <td><b>@item.Punchouttime</b></td>
                <td>@item.SumOfPunching</td>
            </tr>
        }
    </tbody>
</table>
</div>
