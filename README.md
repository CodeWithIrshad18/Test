<tfoot>
    <tr class="fw-bold bg-light">
        <td colspan="@fixedCols">TOTAL</td>

        @{
            // loop through month groups
            for (int i = fixedCols; i < Model.Columns.Count; i += 4)
            {
                decimal totalActualWt = 0;

                foreach (System.Data.DataRow row in Model.Rows)
                {
                    if (row[i + 3] != DBNull.Value)
                    {
                        totalActualWt += Convert.ToDecimal(row[i + 3]);
                    }
                }
        }

                <td></td>   <!-- Monthly Target -->
                <td></td>   <!-- Actual -->
                <td></td>   <!-- % -->
                <td>@totalActualWt.ToString("0.00")</td> <!-- Actual Wt -->
        @{
            }
        }
    </tr>
</tfoot>

tfoot tr {
    background-color: #f2f2f2;
    font-weight: bold;
}




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
