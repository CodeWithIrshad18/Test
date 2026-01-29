public IActionResult TPRCalculationReport(string Dept, string FinYear)
{
    ViewBag.Dept = GetDept();
    ViewBag.FinYearDD = GetFinYearDD();

    DataTable dt = GetTPRReportData(Dept, FinYear);

    // Calculate month totals
    List<decimal> monthTotals = new List<decimal>();

    int fixedCols = 7;

    for (int i = fixedCols; i < dt.Columns.Count; i += 4)
    {
        decimal total = 0;

        foreach (DataRow row in dt.Rows)
        {
            if (row[i + 3] != DBNull.Value)
                total += Convert.ToDecimal(row[i + 3]);
        }

        monthTotals.Add(total);
    }

    // Get payout slabs
    List<DataTable> payoutList = new List<DataTable>();

    foreach (var total in monthTotals)
    {
        payoutList.Add(GetPayoutSlab(total));
    }

    ViewBag.MonthTotals = monthTotals;
    ViewBag.PayoutList = payoutList;

    return View(dt);
}

private DataTable GetPayoutSlab(decimal totalPercent)
{
    DataTable dt = new DataTable();
    string connStr = GetSAPConnectionString();

    using (SqlConnection con = new SqlConnection(connStr))
    {
        string query = @"
        SELECT Group1, Group2, Group3
        FROM App_Payout_NOPR
        WHERE @Total BETWEEN MinRange AND MaxRange
          AND PayoutType = 'Monthly'";

        using (SqlCommand cmd = new SqlCommand(query, con))
        {
            cmd.Parameters.AddWithValue("@Total", totalPercent);
            SqlDataAdapter da = new SqlDataAdapter(cmd);
            da.Fill(dt);
        }
    }

    return dt;
}


@using System.Data

<tfoot>
    <!-- TOTAL row -->
    <tr class="fw-bold bg-light">
        <td colspan="@fixedCols">TOTAL</td>

        @foreach (decimal t in (List<decimal>)ViewBag.MonthTotals)
        {
            <td></td>
            <td></td>
            <td></td>
            <td>@t.ToString("0.00")%</td>
        }
    </tr>

    <!-- GROUP row -->
    <tr class="fw-bold">
        <td colspan="@fixedCols">Group</td>

        @foreach (decimal t in (List<decimal>)ViewBag.MonthTotals)
        {
            <td>G-1</td>
            <td>G-2</td>
            <td>G-3</td>
            <td></td>
        }
    </tr>

    <!-- PAYOUT row -->
    <tr class="fw-bold">
        <td colspan="@fixedCols">Payout</td>

        @{
            var payouts = (List<DataTable>)ViewBag.PayoutList;

            foreach (var p in payouts)
            {
                <td>@p.Rows[0]["Group1"]</td>
                <td>@p.Rows[0]["Group2"]</td>
                <td>@p.Rows[0]["Group3"]</td>
                <td></td>
            }
        }
    </tr>
</tfoot>




The type or namespace name 'DataRow' could not be found (are you missing a using directive or an assembly reference?)
The name 'GetPayoutSlab' does not exist in the current context



<div class="table-scroll">
    <table class="table table-bordered table-sm text-center modern-table freeze-table">

        <thead>
            <tr>
                @* Fixed columns *@
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

        <tfoot>
            <tr class="fw-bold bg-light">
                <td colspan="@fixedCols">TOTAL</td>

                @for (int i = fixedCols; i < Model.Columns.Count; i += 4)
                {
                    decimal totalActualWt = 0;

                    foreach (System.Data.DataRow row in Model.Rows)
                    {
                        if (row[i + 3] != DBNull.Value)
                        {
                            totalActualWt += Convert.ToDecimal(row[i + 3]);
                        }
                    }

                    <td></td>   <!-- Monthly Target -->
                    <td></td>   <!-- Actual -->
                    <td></td>   <!-- % -->
                    <td>@totalActualWt.ToString("0.00")</td> <!-- Actual Wt -->
                }
            </tr>
        </tfoot>

    </table>
