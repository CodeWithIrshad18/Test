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

public IActionResult TPRCalculationReport(string Dept,string FinYear)
{
    ViewBag.Dept = GetDept();
    ViewBag.FinYearDD = GetFinYearDD();

    ViewBag.DeptData = Dept;
    ViewBag.FinYearData = FinYear;

    DataTable dt = GetTPRReportData(Dept,FinYear);

    return View(dt);
}

<div class="col-md-3">
    <select class="form-select form-select-sm" name="FinYear" onchange="this.form.submit()">
        <option value="">FinYear</option>
        @foreach (var item in FinYearDropdown)
        {
            <option value="@item.ID" selected="@(item.FinYear == ViewBag.FinYearData ? "selected" : null)">
                @item.FinYear
            </option>
        }
    </select>
</div>
