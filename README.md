select * from App_Payout_NOPR



ID	MinRange	MaxRange	Group1	Group2	Group3	PayoutType
75C4C33B-397C-4796-9C4B-2F561E4F86AC	95.00	99.99	760	1000	1235	Monthly
BA59CAC5-EB54-4F62-93F3-34E921927B18	80.00	84.99	640	840	1040	Monthly
4C3AB098-6910-48B2-AD6A-3C27F86ADF00	90.00	94.99	720	945	1170	Monthly
FBA953B8-002B-4E3F-B8B2-3E965C87F006	100.00	104.99	850	1100	1350	Monthly
B7E377B7-2D87-46C7-8E60-781D2635937E	0.00	80.00	0	0	0	Monthly
1A60ECAB-ED1D-41AA-915B-A6F89FAB3BFA	105.00	109.99	950	1200	1450	Monthly
148CEEB1-C74F-47E4-8234-B84629E1CC72	85.00	89.99	680	890	1105	Monthly
77D7F4C1-B68F-4F2C-A0BC-EF5558BD8699	110.00	9999.99	1050	1300	1550	Monthly



TOTAL								92.56%				90.06%				84.71%
Group						G-1	G-2	G-3		G-1	G-2	G-3		G-1	G-2	G-3
Payout						720	945	1170		720	945	1170		640	840	1040



        public IActionResult DownloadDynamicTPRReport(string Dept, string FinYear)
        {
            DataTable dt = GetTPRReportData(Dept, FinYear);

            using (var wb = new XLWorkbook())
            {
                var ws = wb.Worksheets.Add("TPR Achievement");

                int fixedCols = 7; 
                int headerRow1 = 1;
                int headerRow2 = 2;
                int dataStartRow = 3;

                
                ws.Cell(1, 1).Value = "TPR Achievement";
                ws.Range(1, 1, 1, dt.Columns.Count).Merge();
                ws.Row(1).Style.Font.Bold = true;
                ws.Row(1).Style.Font.FontSize = 14;
                ws.Row(1).Style.Alignment.Horizontal = XLAlignmentHorizontalValues.Center;
                ws.Row(1).Style.Fill.BackgroundColor = XLColor.LightGray;

                
                for (int i = 0; i < fixedCols; i++)
                {
                    ws.Cell(2, i + 1).Value = dt.Columns[i].ColumnName;
                    ws.Range(2, i + 1, 3, i + 1).Merge();
                    ws.Cell(2, i + 1).Style.Font.Bold = true;
                    ws.Cell(2, i + 1).Style.Alignment.Horizontal = XLAlignmentHorizontalValues.Center;
                    ws.Cell(2, i + 1).Style.Alignment.Vertical = XLAlignmentVerticalValues.Center;
                    ws.Cell(2, i + 1).Style.Fill.BackgroundColor = XLColor.LightBlue;
                }

              
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

              
                ws.Cell(dataStartRow + 1, 1).InsertData(dt.AsEnumerable());

                int lastDataRow = dataStartRow + dt.Rows.Count;

               
                ws.Cell(lastDataRow + 1, 1).Value = "TOTAL";
                ws.Range(lastDataRow + 1, 1, lastDataRow + 1, fixedCols).Merge();
                ws.Range(lastDataRow + 1, 1, lastDataRow + 1, fixedCols).Style.Font.Bold = true;

               
                for (int c = fixedCols + 4; c <= dt.Columns.Count; c += 4)
                {
                    ws.Cell(lastDataRow + 1, c).FormulaA1 =
                        $"SUM({ws.Cell(dataStartRow + 1, c).Address}:{ws.Cell(lastDataRow, c).Address})";
                }

                ws.Range(lastDataRow + 1, 1, lastDataRow + 1, dt.Columns.Count).Style.Fill.BackgroundColor = XLColor.LightGray;
                ws.Range(lastDataRow + 1, 1, lastDataRow + 1, dt.Columns.Count).Style.Font.Bold = true;

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
