public class KPIReportVM
{
    public string KPICode { get; set; }
    public string KPIDetails { get; set; }
    public string Division { get; set; }
    public string Department { get; set; }
    public string Section { get; set; }
    public string TypeofKPI { get; set; }
    public string UnitCode { get; set; }
    public string PeriodicityName { get; set; }
    public string Month { get; set; }
    public decimal? TargetValue { get; set; }
    public decimal? ActualValue { get; set; }
    public decimal? YTDValue { get; set; }
}

SELECT  
    km.KPICode,
    km.KPIDetails,
    km.Division,
    km.Department,
    km.Section,
    t.TypeofKPI,
    u.UnitCode,
    p.PeriodicityName,
    pt.PeriodicityName AS Month,
    tsd.TargetValue,
    kd.Value AS ActualValue,
    kd.YTDValue
FROM App_KPIMaster_NOPR km
LEFT JOIN App_TargetSetting_NOPR ts ON ts.KPIID = km.ID
LEFT JOIN App_UOM_NOPR u ON km.UnitID = u.ID
LEFT JOIN App_PeriodicityMaster_NOPR p ON km.PeriodicityID = p.ID
LEFT JOIN App_TargetSettingDetails_NOPR tsd ON tsd.MasterID = ts.ID
LEFT JOIN App_TypeofKPI_NOPR t ON km.TypeofKPIID = t.ID
LEFT JOIN App_PeriodicityTransaction_NOPR pt 
       ON pt.PeriodicityName = tsd.PeriodicityTransactionID
      AND pt.PeriodicityID = ts.PeriodicityID
LEFT JOIN App_KPIDetails_NOPR kd
       ON kd.KPIID = km.ID
      AND kd.PeriodTransactionID = pt.ID
ORDER BY km.KPICode;



public List<KPIReportVM> GetKpiReportData()
{
    List<KPIReportVM> list = new();

    using (SqlConnection con = new SqlConnection(_connectionString))
    {
        string query = @"PUT YOUR QUERY HERE";

        using (SqlCommand cmd = new SqlCommand(query, con))
        {
            con.Open();
            using (SqlDataReader dr = cmd.ExecuteReader())
            {
                while (dr.Read())
                {
                    list.Add(new KPIReportVM
                    {
                        KPICode = dr["KPICode"].ToString(),
                        KPIDetails = dr["KPIDetails"].ToString(),
                        Division = dr["Division"].ToString(),
                        Department = dr["Department"].ToString(),
                        Section = dr["Section"].ToString(),
                        TypeofKPI = dr["TypeofKPI"].ToString(),
                        UnitCode = dr["UnitCode"].ToString(),
                        PeriodicityName = dr["PeriodicityName"].ToString(),
                        Month = dr["Month"].ToString(),
                        TargetValue = dr["TargetValue"] as decimal?,
                        ActualValue = dr["ActualValue"] as decimal?,
                        YTDValue = dr["YTDValue"] as decimal?
                    });
                }
            }
        }
    }

    return list;
}

public class KPIReportController : Controller
{
    public IActionResult Index()
    {
        var data = GetKpiReportData();
        return View(data);   // shows report
    }

    public IActionResult ExportExcel()
    {
        var data = GetKpiReportData();
        return GenerateExcel(data);
    }

    public IActionResult ExportPdf()
    {
        var data = GetKpiReportData();
        return GeneratePdf(data);
    }
}

<h3>KPI Report</h3>

<a asp-action="ExportExcel" class="btn btn-success">Download Excel</a>
<a asp-action="ExportPdf" class="btn btn-danger">Download PDF</a>

<table class="table table-bordered mt-3">
    <thead>
        <tr>
            <th>KPI Code</th>
            <th>Month</th>
            <th>Target</th>
            <th>Actual</th>
            <th>YTD</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var item in Model)
        {
            <tr>
                <td>@item.KPICode</td>
                <td>@item.Month</td>
                <td>@item.TargetValue</td>
                <td>@item.ActualValue</td>
                <td>@item.YTDValue</td>
            </tr>
        }
    </tbody>
</table>




SELECT  
    km.KPICode,
    km.KPIDetails,
    km.Division,
    km.Department,
    km.Section,
    t.TypeofKPI,
    u.UnitCode,
    p.PeriodicityName,
    pt.PeriodicityName AS Month,
    tsd.TargetValue,
    kd.Value AS ActualValue,
    kd.YTDValue

FROM App_KPIMaster_NOPR km

LEFT JOIN App_TargetSetting_NOPR ts
    ON ts.KPIID = km.ID

    LEFT JOIN App_UOM_NOPR u 
    ON km.UnitID = u.ID

    LEFT JOIN App_PeriodicityMaster_NOPR p 
    ON km.PeriodicityID = p.ID

LEFT JOIN App_TargetSettingDetails_NOPR tsd
    ON tsd.MasterID = ts.ID

LEFT JOIN App_TypeofKPI_NOPR t ON km.TypeofKPIID = t.ID

LEFT JOIN App_PeriodicityTransaction_NOPR pt
    ON pt.PeriodicityName = tsd.PeriodicityTransactionID
   AND pt.PeriodicityID = ts.PeriodicityID

LEFT JOIN App_KPIDetails_NOPR kd
    ON kd.KPIID = km.ID
   AND kd.PeriodTransactionID = pt.ID   
ORDER BY km.KPICode;
