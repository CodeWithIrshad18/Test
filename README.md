public IActionResult DownloadDynamicTPRReport(string Dept, string FinYear)
{
    DataTable dt = GetTPRReportData(Dept, FinYear);

    using (var wb = new XLWorkbook())
    {
        var ws = wb.Worksheets.Add("TPR Achievement");

        int fixedCols = 7; // KPI#, Areas, Dept, Type, UOM, Good Perf, Weightage
        int headerRow1 = 1;
        int headerRow2 = 2;
        int dataStartRow = 3;

        // ===== TITLE =====
        ws.Cell(1, 1).Value = "TPR Achievement";
        ws.Range(1, 1, 1, dt.Columns.Count).Merge();
        ws.Row(1).Style.Font.Bold = true;
        ws.Row(1).Style.Font.FontSize = 14;
        ws.Row(1).Style.Alignment.Horizontal = XLAlignmentHorizontalValues.Center;
        ws.Row(1).Style.Fill.BackgroundColor = XLColor.LightGray;

        // ===== FIXED HEADERS =====
        for (int i = 0; i < fixedCols; i++)
        {
            ws.Cell(2, i + 1).Value = dt.Columns[i].ColumnName;
            ws.Range(2, i + 1, 3, i + 1).Merge();
            ws.Cell(2, i + 1).Style.Font.Bold = true;
            ws.Cell(2, i + 1).Style.Alignment.Horizontal = XLAlignmentHorizontalValues.Center;
            ws.Cell(2, i + 1).Style.Alignment.Vertical = XLAlignmentVerticalValues.Center;
            ws.Cell(2, i + 1).Style.Fill.BackgroundColor = XLColor.LightBlue;
        }

        // ===== MONTH HEADERS =====
        List<string> months = new List<string>();

        for (int i = fixedCols; i < dt.Columns.Count; i += 4)
        {
            string month = dt.Columns[i].ColumnName.Split(' ')[0];
            months.Add(month);
        }

        int colIndex = fixedCols + 1;

        foreach (var month in months)
        {
            ws.Range(2, colIndex, 2, colIndex + 3).Merge().Value = month;
            ws.Range(2, colIndex, 2, colIndex + 3).Style.Font.Bold = true;
            ws.Range(2, colIndex, 2, colIndex + 3).Style.Alignment.Horizontal = XLAlignmentHorizontalValues.Center;
            ws.Range(2, colIndex, 2, colIndex + 3).Style.Fill.BackgroundColor = XLColor.LightSalmon;

            ws.Cell(3, colIndex).Value = "Monthly Target";
            ws.Cell(3, colIndex + 1).Value = "Actual";
            ws.Cell(3, colIndex + 2).Value = "%";
            ws.Cell(3, colIndex + 3).Value = "Actual Wt.";

            ws.Range(3, colIndex, 3, colIndex + 3).Style.Font.Bold = true;
            ws.Range(3, colIndex, 3, colIndex + 3).Style.Alignment.Horizontal = XLAlignmentHorizontalValues.Center;
            ws.Range(3, colIndex, 3, colIndex + 3).Style.Fill.BackgroundColor = XLColor.LightGoldenrodYellow;

            colIndex += 4;
        }

        // ===== INSERT DATA =====
        ws.Cell(dataStartRow + 1, 1).InsertData(dt.AsEnumerable());

        int lastDataRow = dataStartRow + dt.Rows.Count;

        // ===== TOTAL ROW =====
        ws.Cell(lastDataRow + 1, 1).Value = "TOTAL";
        ws.Range(lastDataRow + 1, 1, lastDataRow + 1, fixedCols).Merge();
        ws.Range(lastDataRow + 1, 1, lastDataRow + 1, fixedCols).Style.Font.Bold = true;

        // Sum only "Actual Wt." columns
        for (int c = fixedCols + 4; c <= dt.Columns.Count; c += 4)
        {
            ws.Cell(lastDataRow + 1, c).FormulaA1 =
                $"SUM({ws.Cell(dataStartRow + 1, c).Address}:{ws.Cell(lastDataRow, c).Address})";
        }

        ws.Range(lastDataRow + 1, 1, lastDataRow + 1, dt.Columns.Count).Style.Fill.BackgroundColor = XLColor.LightGray;
        ws.Range(lastDataRow + 1, 1, lastDataRow + 1, dt.Columns.Count).Style.Font.Bold = true;

        // ===== STYLING =====
        ws.Range(1, 1, lastDataRow + 1, dt.Columns.Count).Style.Border.OutsideBorder = XLBorderStyleValues.Thin;
        ws.Range(1, 1, lastDataRow + 1, dt.Columns.Count).Style.Border.InsideBorder = XLBorderStyleValues.Thin;

        ws.Range(4, 1, lastDataRow, dt.Columns.Count).Style.Alignment.Horizontal = XLAlignmentHorizontalValues.Center;

        ws.Columns().AdjustToContents();
        ws.SheetView.FreezeRows(3);

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

        
        
        
        
        public IActionResult DownloadDynamicTPRReport(string Dept, string FinYear)
        {
            DataTable dt = GetTPRReportData(Dept,FinYear);

            using (var wb = new XLWorkbook())
            {
                var ws = wb.Worksheets.Add("TPR Achievement");

                ws.Cell(1, 1).InsertTable(dt);
                ws.Columns().AdjustToContents();

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


        public IActionResult TPRCalculationReport(string Dept,string FinYear)
        {
            ViewBag.Dept = GetDept();
            ViewBag.FinYearDD = GetFinYearDD();

            ViewBag.DeptData = Dept;
            ViewBag.FinYearData = FinYear;

            DataTable dt = GetTPRReportData(Dept,FinYear);

            return View(dt);
        }



        private DataTable GetTPRReportData(string Dept,string FinYear)
        {
            DataTable dt = new DataTable();
            string connStr = GetSAPConnectionString();

            ViewBag.DeptData = Dept;

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

            return dt;
        }
