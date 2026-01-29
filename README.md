public IActionResult TPRCalculationReport(string Dept)
{
    ViewBag.Dept = GetDept();

    DataTable dt = new DataTable();

    string connStr = "Server=10.0.168.50;Database=KPIMSTSUISLDB;User Id=fs;Password=p@ssW0Rd321;TrustServerCertificate=yes";

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

WHERE (@DeptFilter = '' OR km.Department = @DeptFilter)

  
  
  
  

public IActionResult TPRCalculationReport()
  {
      ViewBag.Dept = GetDept();


      string Dept = "";

      DataTable dt = new DataTable();

      string connStr = "Server=10.0.168.50;Database=KPIMSTSUISLDB;User Id=fs;Password=p@ssW0Rd321;TrustServerCertificate=yes";

      using (SqlConnection con = new SqlConnection(connStr))
      {
          using (SqlCommand cmd = new SqlCommand("DECLARE @cols AS NVARCHAR(MAX),\r\n        @query AS NVARCHAR(MAX),\r\n        @DeptName AS NVARCHAR(100) = 'Human Resource & Industrial Relations';\r\n\r\nSELECT @cols = STUFF((\r\n    SELECT ',' + QUOTENAME(PeriodicityName + ' Target') \r\n               + ',' + QUOTENAME(PeriodicityName + ' Actual')\r\n               + ',' + QUOTENAME(PeriodicityName + ' Perf %')\r\n               + ',' + QUOTENAME(PeriodicityName + ' Act Weight')\r\n    FROM App_PeriodicityTransaction_NOPR    where PeriodicityID='CDD263FE-5946-4BD2-A2C7-B7E31C19640A'\r\n    GROUP BY PeriodicityName, Sl_no\r\n    ORDER BY Sl_no\r\n    FOR XML PATH(''), TYPE).value('.', 'NVARCHAR(MAX)'), 1, 1, '');\r\n\r\nSET @query = '\r\nDECLARE @TotalKPIs FLOAT;\r\n-- Filter added here to calculate @BaseWeight specifically for TPR\r\nSELECT @TotalKPIs = COUNT(*) FROM App_KPIMaster_NOPR km \r\nJOIN App_TypeofKPI_NOPR t ON km.TypeofKPIID = t.ID \r\nWHERE km.Department = @DeptFilter AND t.TypeofKPI = ''TPR'';\r\n\r\n-- Base Weightage (e.g., 4.76% if 21 KPIs)\r\nDECLARE @BaseWeight FLOAT = 100.0 / NULLIF(@TotalKPIs, 0);\r\n\r\nSELECT \r\n    KPICode, KPIDetails, Department, TypeofKPI, UnitCode, GoodPerformance,\r\n    CAST(@BaseWeight AS DECIMAL(18,2)) AS [Weightage (%)],\r\n    ' + @cols + '\r\nFROM \r\n(\r\n    SELECT  \r\n        km.KPICode, km.KPIDetails, km.Department,\r\n        t.TypeofKPI, u.UnitCode, gn.Name AS GoodPerformance,\r\n        pt.PeriodicityName + '' '' + val.ValueType AS ColumnHeader,\r\n        CAST(val.Value AS DECIMAL(18,2)) AS Value\r\n    FROM App_KPIMaster_NOPR km\r\n    LEFT JOIN App_UOM_NOPR u ON km.UnitID = u.ID\r\n    LEFT JOIN App_TypeofKPI_NOPR t ON km.TypeofKPIID = t.ID\r\n    LEFT JOIN App_GoodPerformance_NOPR gn ON gn.ID = km.GoodPerformance\r\n    LEFT JOIN App_TargetSetting_NOPR ts ON ts.KPIID = km.ID\r\n    LEFT JOIN App_TargetSettingDetails_NOPR tsd ON tsd.MasterID = ts.ID\r\n    LEFT JOIN App_PeriodicityTransaction_NOPR pt \r\n           ON pt.PeriodicityName = tsd.PeriodicityTransactionID\r\n          AND pt.PeriodicityID = ts.PeriodicityID\r\n    LEFT JOIN App_KPIDetails_NOPR kd \r\n           ON kd.KPIID = km.ID \r\n          AND kd.PeriodTransactionID = pt.ID\r\n    CROSS APPLY (\r\n        SELECT \r\n            CAST(ISNULL(tsd.TargetValue,0) AS FLOAT) as Tgt, \r\n            CAST(ISNULL(kd.Value,0) AS FLOAT) as Act,\r\n            CASE \r\n                WHEN gn.Name LIKE ''%Higher%'' THEN (CAST(ISNULL(kd.Value,0) AS FLOAT) / NULLIF(CAST(tsd.TargetValue AS FLOAT), 0)) * 100\r\n                WHEN gn.Name LIKE ''%Lower%'' THEN (CAST(ISNULL(tsd.TargetValue,0) AS FLOAT) / NULLIF(CAST(kd.Value AS FLOAT), 0)) * 100\r\n                ELSE 0 \r\n            END AS PerfPct\r\n    ) calc\r\n    CROSS APPLY (\r\n        SELECT ''Target'' AS ValueType, calc.Tgt AS Value\r\n        UNION ALL\r\n        SELECT ''Actual'' AS ValueType, calc.Act AS Value\r\n        UNION ALL\r\n        SELECT ''Perf %'' AS ValueType, calc.PerfPct AS Value\r\n        UNION ALL\r\n        -- REVISED INTERNAL LOGIC 4\r\n        SELECT ''Act Weight'' AS ValueType,\r\n            CASE \r\n                -- If Perf % <= 100%: Simple linear weightage\r\n                WHEN calc.PerfPct <= 100 THEN (calc.PerfPct / 100.0) * @BaseWeight\r\n                \r\n                -- If Perf % > 100%: [100% + (25% * (Perf% - 100%))] * Weightage%\r\n                -- Math: ((100 + (0.25 * (Perf - 100))) / 100) * BaseWeight\r\n                WHEN calc.PerfPct > 100 THEN (100.0 + (0.25 * (calc.PerfPct - 100.0))) * (@BaseWeight / 100.0)\r\n                \r\n                ELSE 0 \r\n            END AS Value\r\n    ) val\r\n    WHERE km.Department = @DeptFilter AND t.TypeofKPI = ''TPR''\r\n) x\r\nPIVOT \r\n(\r\n    MAX(Value) \r\n    FOR ColumnHeader IN (' + @cols + ')\r\n) p\r\nORDER BY KPICode;';\r\nEXEC sp_executesql @query, N'@DeptFilter NVARCHAR(100)', @DeptFilter = @DeptName;", con))
          {
              cmd.CommandType = CommandType.Text;
              con.Open();
              SqlDataAdapter da = new SqlDataAdapter(cmd);
              da.Fill(dt);
          }
      }

      return View(dt);
  }


  public List<Department> GetDept()
  {
      string connectionString = GetSAPConnectionString();

      string query = @"SELECT DISTINCT ema_exec_head_desc, ema_dept_desc
          FROM SAPHRDB.dbo.T_Empl_All
          WHERE ema_dept_desc IS NOT NULL
          ORDER BY ema_dept_desc";


      using (var connection = new SqlConnection(connectionString))
      {
          var Dept = connection.Query<Department>(query).ToList();

          return Dept;

      }

  }


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
