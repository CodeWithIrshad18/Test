        public IActionResult DownloadDynamicTPRReport(string Dept)
        {
            DataTable dt = new DataTable();

            string connStr = GetSAPConnectionString();

            using (SqlConnection con = new SqlConnection(connStr))
            {
                string query = @"DECLARE @cols AS NVARCHAR(MAX),
        @query AS NVARCHAR(MAX);

SELECT @cols = STUFF((
    SELECT ',' + QUOTENAME(PeriodicityName + ' Target') 
           + ',' + QUOTENAME(PeriodicityName + ' Actual')
           + ',' + QUOTENAME(PeriodicityName + ' Perf %')
           + ',' + QUOTENAME(PeriodicityName + ' Act Weight')
    FROM App_PeriodicityTransaction_NOPR  
    WHERE PeriodicityID='CDD263FE-5946-4BD2-A2C7-B7E31C19640A'
    GROUP BY PeriodicityName, Sl_no
    ORDER BY Sl_no
    FOR XML PATH(''), TYPE).value('.', 'NVARCHAR(MAX)'), 1, 1, '');

SET @query = '
DECLARE @TotalKPIs FLOAT;

SELECT @TotalKPIs = COUNT(*) 
FROM App_KPIMaster_NOPR km 
JOIN App_TypeofKPI_NOPR t ON km.TypeofKPIID = t.ID 
WHERE km.Department = @DeptFilter AND t.TypeofKPI = ''TPR'';

DECLARE @BaseWeight FLOAT = 100.0 / NULLIF(@TotalKPIs, 0);

SELECT KPICode, KPIDetails, Department, TypeofKPI, UnitCode, GoodPerformance,
CAST(@BaseWeight AS DECIMAL(18,2)) AS [Weightage (%)],
' + @cols + '
FROM (
   SELECT km.KPICode, km.KPIDetails, km.Department,
          t.TypeofKPI, u.UnitCode, gn.Name AS GoodPerformance,
          pt.PeriodicityName + '' '' + val.ValueType AS ColumnHeader,
          CAST(val.Value AS DECIMAL(18,2)) AS Value
   FROM App_KPIMaster_NOPR km
   LEFT JOIN App_UOM_NOPR u ON km.UnitID = u.ID
   LEFT JOIN App_TypeofKPI_NOPR t ON km.TypeofKPIID = t.ID
   LEFT JOIN App_GoodPerformance_NOPR gn ON gn.ID = km.GoodPerformance
   LEFT JOIN App_TargetSetting_NOPR ts ON ts.KPIID = km.ID
   LEFT JOIN App_TargetSettingDetails_NOPR tsd ON tsd.MasterID = ts.ID
   LEFT JOIN App_PeriodicityTransaction_NOPR pt 
        ON pt.PeriodicityName = tsd.PeriodicityTransactionID
       AND pt.PeriodicityID = ts.PeriodicityID
   LEFT JOIN App_KPIDetails_NOPR kd 
        ON kd.KPIID = km.ID 
       AND kd.PeriodTransactionID = pt.ID
   CROSS APPLY (
        SELECT 
            CAST(ISNULL(tsd.TargetValue,0) AS FLOAT) as Tgt, 
            CAST(ISNULL(kd.Value,0) AS FLOAT) as Act,
            CASE 
                WHEN gn.Name LIKE ''%Higher%'' THEN (CAST(ISNULL(kd.Value,0) AS FLOAT) / NULLIF(CAST(tsd.TargetValue AS FLOAT), 0)) * 100
                WHEN gn.Name LIKE ''%Lower%'' THEN (CAST(ISNULL(tsd.TargetValue,0) AS FLOAT) / NULLIF(CAST(kd.Value AS FLOAT), 0)) * 100
                ELSE 0 
            END AS PerfPct
   ) calc
   CROSS APPLY (
        SELECT ''Target'' AS ValueType, calc.Tgt AS Value
        UNION ALL
        SELECT ''Actual'', calc.Act
        UNION ALL
        SELECT ''Perf %'', calc.PerfPct
        UNION ALL
        SELECT ''Act Weight'',
            CASE 
                WHEN calc.PerfPct <= 100 THEN (calc.PerfPct / 100.0) * @BaseWeight
                WHEN calc.PerfPct > 100 THEN (100.0 + (0.25 * (calc.PerfPct - 100.0))) * (@BaseWeight / 100.0)
                ELSE 0 
            END
   ) val
   WHERE km.Department = @DeptFilter AND t.TypeofKPI = ''TPR''
) x
PIVOT (MAX(Value) FOR ColumnHeader IN (' + @cols + ')) p
ORDER BY KPICode';

EXEC sp_executesql @query, N'@DeptFilter NVARCHAR(100)', @DeptFilter = @DeptFilter;";

                using (SqlCommand cmd = new SqlCommand(query, con))
                {
                    cmd.Parameters.AddWithValue("@DeptFilter", Dept ?? "");

                    SqlDataAdapter da = new SqlDataAdapter(cmd);
                    da.Fill(dt);
                }
            }

            using (var wb = new XLWorkbook())
            {
                var ws = wb.Worksheets.Add("TPR Achievement");

                int fixedCols = 9;

                string[] fixedHeaders = {
            "KPI Code","KPI Details","Department",
            "Type of KPI","UOM","Good Performance","Weightage (%)"
        };

                for (int i = 0; i < fixedHeaders.Length; i++)
                {
                    ws.Cell(1, i + 1).Value = fixedHeaders[i];
                    ws.Range(1, i + 1, 2, i + 1).Merge();
                }


                List<string> months = new List<string>();

                for (int i = fixedCols; i < dt.Columns.Count; i += 4)
                {
                    string colName = dt.Columns[i].ColumnName; 
                    string month = colName.Split(' ')[0];   
                    months.Add(month);
                }

                int colIndex = fixedCols + 1;

                foreach (var month in months)
                {
                    ws.Range(1, colIndex, 1, colIndex + 3).Merge().Value = month;

                    ws.Cell(2, colIndex).Value = "Monthly Target";
                    ws.Cell(2, colIndex + 1).Value = "Actual";
                    ws.Cell(2, colIndex + 2).Value = "%";
                    ws.Cell(2, colIndex + 3).Value = "Actual Wt.";

                    colIndex += 4;
                }



                ws.Cell(3, 1).InsertTable(dt);

                int lastCol = dt.Columns.Count;


                ws.Range(1, 1, 2, lastCol).Style.Font.Bold = true;
                ws.Range(1, 1, 2, lastCol).Style.Alignment.Horizontal = XLAlignmentHorizontalValues.Center;
                ws.Range(1, 1, 2, lastCol).Style.Alignment.Vertical = XLAlignmentVerticalValues.Center;
                ws.Range(1, 1, 2, lastCol).Style.Fill.BackgroundColor = XLColor.LightGray;

                ws.RangeUsed().Style.Border.OutsideBorder = XLBorderStyleValues.Thin;
                ws.RangeUsed().Style.Border.InsideBorder = XLBorderStyleValues.Thin;

                ws.Columns().AdjustToContents();
                ws.SheetView.FreezeRows(2);


                using (var stream = new MemoryStream())
                {
                    wb.SaveAs(stream);
                    stream.Position = 0;

                    return File(stream.ToArray(),
                        "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",
                        "TPR_Report.xlsx");
                }
            }
        }

        public IActionResult TPRCalculationReport(string Dept)
        {
            ViewBag.Dept = GetDept();

            DataTable dt = new DataTable();

            string connStr = GetSAPConnectionString();

            using (SqlConnection con = new SqlConnection(connStr))
            {
                string query = @"DECLARE @cols AS NVARCHAR(MAX),
        @query AS NVARCHAR(MAX);

SELECT @cols = STUFF((
    SELECT ',' + QUOTENAME(PeriodicityName + ' Target') 
           + ',' + QUOTENAME(PeriodicityName + ' Actual')
           + ',' + QUOTENAME(PeriodicityName + ' Perf %')
           + ',' + QUOTENAME(PeriodicityName + ' Act Weight')
    FROM App_PeriodicityTransaction_NOPR  
    WHERE PeriodicityID='CDD263FE-5946-4BD2-A2C7-B7E31C19640A'
    GROUP BY PeriodicityName, Sl_no
    ORDER BY Sl_no
    FOR XML PATH(''), TYPE).value('.', 'NVARCHAR(MAX)'), 1, 1, '');

SET @query = '
DECLARE @TotalKPIs FLOAT;

SELECT @TotalKPIs = COUNT(*) 
FROM App_KPIMaster_NOPR km 
JOIN App_TypeofKPI_NOPR t ON km.TypeofKPIID = t.ID 
WHERE km.Department = @DeptFilter AND t.TypeofKPI = ''TPR'';

DECLARE @BaseWeight FLOAT = 100.0 / NULLIF(@TotalKPIs, 0);

SELECT KPICode, KPIDetails, Department, TypeofKPI, UnitCode, GoodPerformance,
CAST(@BaseWeight AS DECIMAL(18,2)) AS [Weightage (%)],
' + @cols + '
FROM (
   SELECT km.KPICode, km.KPIDetails, km.Department,
          t.TypeofKPI, u.UnitCode, gn.Name AS GoodPerformance,
          pt.PeriodicityName + '' '' + val.ValueType AS ColumnHeader,
          CAST(val.Value AS DECIMAL(18,2)) AS Value
   FROM App_KPIMaster_NOPR km
   LEFT JOIN App_UOM_NOPR u ON km.UnitID = u.ID
   LEFT JOIN App_TypeofKPI_NOPR t ON km.TypeofKPIID = t.ID
   LEFT JOIN App_GoodPerformance_NOPR gn ON gn.ID = km.GoodPerformance
   LEFT JOIN App_TargetSetting_NOPR ts ON ts.KPIID = km.ID
   LEFT JOIN App_TargetSettingDetails_NOPR tsd ON tsd.MasterID = ts.ID
   LEFT JOIN App_PeriodicityTransaction_NOPR pt 
        ON pt.PeriodicityName = tsd.PeriodicityTransactionID
       AND pt.PeriodicityID = ts.PeriodicityID
   LEFT JOIN App_KPIDetails_NOPR kd 
        ON kd.KPIID = km.ID 
       AND kd.PeriodTransactionID = pt.ID
   CROSS APPLY (
        SELECT 
            CAST(ISNULL(tsd.TargetValue,0) AS FLOAT) as Tgt, 
            CAST(ISNULL(kd.Value,0) AS FLOAT) as Act,
            CASE 
                WHEN gn.Name LIKE ''%Higher%'' THEN (CAST(ISNULL(kd.Value,0) AS FLOAT) / NULLIF(CAST(tsd.TargetValue AS FLOAT), 0)) * 100
                WHEN gn.Name LIKE ''%Lower%'' THEN (CAST(ISNULL(tsd.TargetValue,0) AS FLOAT) / NULLIF(CAST(kd.Value AS FLOAT), 0)) * 100
                ELSE 0 
            END AS PerfPct
   ) calc
   CROSS APPLY (
        SELECT ''Target'' AS ValueType, calc.Tgt AS Value
        UNION ALL
        SELECT ''Actual'', calc.Act
        UNION ALL
        SELECT ''Perf %'', calc.PerfPct
        UNION ALL
        SELECT ''Act Weight'',
            CASE 
                WHEN calc.PerfPct <= 100 THEN (calc.PerfPct / 100.0) * @BaseWeight
                WHEN calc.PerfPct > 100 THEN (100.0 + (0.25 * (calc.PerfPct - 100.0))) * (@BaseWeight / 100.0)
                ELSE 0 
            END
   ) val
   WHERE km.Department = @DeptFilter AND t.TypeofKPI = ''TPR''
) x
PIVOT (MAX(Value) FOR ColumnHeader IN (' + @cols + ')) p
ORDER BY KPICode';

EXEC sp_executesql @query, N'@DeptFilter NVARCHAR(100)', @DeptFilter = @DeptFilter;";

                using (SqlCommand cmd = new SqlCommand(query, con))
                {
                    cmd.Parameters.AddWithValue("@DeptFilter", Dept ?? "");

                    SqlDataAdapter da = new SqlDataAdapter(cmd);
                    da.Fill(dt);
                }
            }

            return View(dt);
        }


<div class="container-fluid mt-4">

    <div class="card shadow-sm border-0">
        <div class="card-header bg-white py-3 d-flex justify-content-between align-items-center">
            <div>
                <h5 class="mb-0 fw-semibold">TPR Calculation Report</h5>
            </div>

           
        </div>

        <div class="card-body">



             <div class="d-flex gap-2">
               <a asp-action="DownloadDynamicTPRReport" asp-route-kpiId="@ViewBag.KpiId" class="btn btn-outline-success btn-sm">
    <i class="bi bi-file-earmark-excel"></i> Excel
</a>

            </div>

                        <form method="get" asp-action="TPRCalculationReport">
    <div class="row g-2 mb-3">
        <div class="col-md-4">
            <select class="form-select form-select-sm" name="Dept" onchange="this.form.submit()">
                <option value="">All Departments</option>
                @foreach (var item in DepartmentDropdown)
                {
                    <option value="@item.ema_dept_desc">
                        @item.ema_dept_desc
                    </option>
                }
            </select>
        </div>
    </div>
</form>
            <div class="table-wrapper">

<div class="table-scroll">
    <table class="table table-bordered table-sm text-center modern-table freeze-table">
    <thead>
        <tr>
  
            @for (int i = 0; i < fixedCols; i++)
            {
                <th rowspan="2">@Model.Columns[i].ColumnName</th>
            }

            @{
                List<string> months = new List<string>();

                for (int i = fixedCols; i < Model.Columns.Count; i += 4)
                {
                    string colName = Model.Columns[i].ColumnName; 
                    string month = colName.Split(' ')[0];
                    months.Add(month);
                }

                foreach (var m in months)
                {
                    <th colspan="4">@m</th>
                }
            }
        </tr>

        <tr>
            @for (int i = 0; i < months.Count; i++)
            {
                <th>Monthly Target</th>
                <th>Actual</th>
                <th>%</th>
                <th>Actual Wt.</th>
            }
        </tr>
    </thead>

    <tbody>
        @foreach (System.Data.DataRow row in Model.Rows)
        {
            <tr>
                @for (int i = 0; i < Model.Columns.Count; i++)
                {
                    <td>@row[i]</td>
                }
            </tr>
        }
    </tbody>
</table>
</div>
</div>
</div>
</div>
