<form method="get" asp-action="Index">
    <div class="row g-2 mb-3">
        <div class="col-md-4">
            <select class="form-select form-select-sm" name="kpiId" onchange="this.form.submit()">
                <option value="">All KPIs</option>
                @foreach (var item in KpiDropdown)
                {
                    <option value="@item.ID" selected="@(item.ID == ViewBag.KpiId ? "selected" : null)">
                        @item.KPIDetails
                    </option>
                }
            </select>
        </div>
    </div>
</form>

public IActionResult Index(string kpiId)
{
    ViewBag.KpiId = kpiId;
    var data = GetKpiReportData(kpiId);
    return View(data);
}

public List<KPIReportVM> GetKpiReportData(string kpiId = null)
{
    List<KPIReportVM> list = new();

    using (SqlConnection con = new SqlConnection(GetSAPConnectionString()))
    {
        string query = @"
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
WHERE (@KpiId IS NULL OR km.ID = @KpiId)
ORDER BY km.KPICode, pt.Sl_no;
";

        using (SqlCommand cmd = new SqlCommand(query, con))
        {
            cmd.Parameters.AddWithValue("@KpiId", (object?)kpiId ?? DBNull.Value);

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
                        TargetValue = dr["TargetValue"]?.ToString(),
                        ActualValue = dr["ActualValue"] as decimal?,
                        YTDValue = dr["YTDValue"] as decimal?
                    });
                }
            }
        }
    }

    return list;
}


public IActionResult ExportExcel(string kpiId)
{
    var data = GetKpiReportData(kpiId);
    return GenerateExcel(data);
}

public IActionResult ExportPdf(string kpiId)
{
    var data = GetKpiReportData(kpiId);
    return GeneratePdf(data);
}

<a asp-action="ExportExcel" asp-route-kpiId="@ViewBag.KpiId" class="btn btn-outline-success btn-sm">
    <i class="bi bi-file-earmark-excel"></i> Excel
</a>

<a asp-action="ExportPdf" asp-route-kpiId="@ViewBag.KpiId" class="btn btn-outline-danger btn-sm">
    <i class="bi bi-file-earmark-pdf"></i> PDF
</a>




<tbody>
@{
    string lastKpi = null;
}

@foreach (var item in Model)
{
    if (lastKpi != item.KPICode)
    {
        <tr class="kpi-group" onclick="toggleKpi('@item.KPICode')">
            <td colspan="8" class="fw-semibold bg-light">
                <span id="icon-@item.KPICode" class="me-2 toggle-icon">+</span>
                @item.KPICode
                <small class="text-muted ms-2">@item.KPIDetails</small>
            </td>
        </tr>

        lastKpi = item.KPICode;
    }

    <tr class="kpi-row kpi-@item.KPICode d-none">
        <td></td>
        <td>@item.Division</td>
        <td>@item.Department</td>
        <td>@item.Section</td>
        <td>@item.Month</td>
        <td>@item.TargetValue</td>
        <td>@item.ActualValue</td>
        <td>@item.YTDValue</td>
    </tr>
}
</tbody>


<script>
    function toggleKpi(kpi) {
        let rows = document.querySelectorAll('.kpi-' + CSS.escape(kpi));
        let icon = document.getElementById('icon-' + kpi);

        if (!rows.length) return;

        let isHidden = rows[0].classList.contains('d-none');

        rows.forEach(row => {
            row.classList.toggle('d-none', !isHidden);
        });

        icon.innerText = isHidden ? '−' : '+';
    }
</script>


<style>
    .kpi-group {
        cursor: pointer;
        background: #f8f9fa;
        border-left: 4px solid #0d6efd;
    }

    .kpi-group:hover {
        background: #eef3ff;
    }

    .toggle-icon {
        font-weight: bold;
        width: 18px;
        display: inline-block;
        text-align: center;
    }

    .kpi-row td {
        padding-left: 30px;
        font-size: 13px;
    }
</style>




<div class="container-fluid mt-4">

    <div class="card shadow-sm border-0">
        <div class="card-header bg-white py-3 d-flex justify-content-between align-items-center">
            <div>
                <h5 class="mb-0 fw-semibold">KPI Performance Report</h5>
            </div>

            <div class="d-flex gap-2">
                <a asp-action="ExportExcel" class="btn btn-outline-success btn-sm">
                    <i class="bi bi-file-earmark-excel"></i> Excel
                </a>
                <a asp-action="ExportPdf" class="btn btn-outline-danger btn-sm">
                    <i class="bi bi-file-earmark-pdf"></i> PDF
                </a>
            </div>
        </div>

        <div class="card-body">

            <!-- Filters -->
            <div class="row g-2 mb-3">
                <div class="col-md-4">
                  <select class="form-select form-select-sm me-2" name="Div">
						<option value="">All KPIs</option>
						@foreach (var item in KpiDropdown)
						{
							<option value="@item.ID" selected="@(item.ID == ViewBag.Div ? "selected" : null)">@item.KPIDetails</option>
						}
						
						</select>
                </div>
              
                <div class="col-md-2">
                    <select class="form-select form-select-sm">
                        <option>All Months</option>
                    </select>
                </div>
            </div>

            <!-- Table -->
            <div class="table-scroll">
                <table class="table table-hover align-middle modern-table" id="kpiTable">
                    <thead>
                        <tr>
                            <th>KPI Code</th>
                             <th>Division</th>
                            <th>Department</th>
                            <th>Section</th>
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
                                <td>
                                    <div class="fw-semibold">@item.KPICode</div>
                                    <small class="text-muted">@item.KPIDetails</small>
                                </td>
                                <td>@item.Division</td>
                                <td>@item.Department</td>
                                <td>@item.Section</td>
                                <td>@item.Month</td>
                                <td>@item.TargetValue</td>
                                <td>@item.ActualValue</td>
                                <td>@item.YTDValue</td>
                            </tr>
                        }
                    </tbody>
                </table>
            </div>

        </div>
    </div>
</div>

        public List<KPIReportVM> GetKpiReportData()
        {
            List<KPIReportVM> list = new();

            using (SqlConnection con = new SqlConnection(GetSAPConnectionString()))
            {
                string query = @"SELECT  
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
ORDER BY km.KPICode,pt.Sl_no;";

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
                                TargetValue = dr["TargetValue"].ToString(),
                                ActualValue = dr["ActualValue"] as decimal?,
                                YTDValue = dr["YTDValue"] as decimal?
                            });
                        }
                    }
                }
            }

            return list;
        }
