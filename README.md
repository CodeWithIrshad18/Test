GFAS.Models.AttendanceReportModel,01-11-2025,"","",0,1
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
