/* Month color themes */
.month-0 { background-color: #fde2e2; } /* APR */
.month-1 { background-color: #e2f0ff; } /* MAY */
.month-2 { background-color: #e2ffe9; } /* JUN */
.month-3 { background-color: #fff3cd; } /* JUL */
.month-4 { background-color: #f3e2ff; } /* AUG */
.month-5 { background-color: #e0f7fa; } /* SEP */
.month-6 { background-color: #ffe0b2; } /* OCT */
.month-7 { background-color: #dcedc8; } /* NOV */
.month-8 { background-color: #f8bbd0; } /* DEC */
.month-9 { background-color: #c5cae9; } /* JAN */
.month-10 { background-color: #b2dfdb; } /* FEB */
.month-11 { background-color: #ffccbc; } /* MAR */

/* keep text readable */
.month-0, .month-1, .month-2, .month-3, .month-4, .month-5,
.month-6, .month-7, .month-8, .month-9, .month-10, .month-11 {
    color: #000;
}

@{
    List<string> months = new List<string>();
    for (int i = fixedCols; i < Model.Columns.Count; i += 4)
    {
        string colName = Model.Columns[i].ColumnName;
        string month = colName.Split(' ')[0];
        months.Add(month);
    }
}

@for (int m = 0; m < months.Count; m++)
{
    <th colspan="4" class="month-@m">@months[m]</th>
}
@for (int m = 0; m < months.Count; m++)
{
    <th class="month-@m">Monthly Target</th>
    <th class="month-@m">Actual</th>
    <th class="month-@m">%</th>
    <th class="month-@m">Actual Wt.</th>
}

@for (int i = 0; i < Model.Columns.Count; i++)
{
    if (i < fixedCols)
    {
        <td>@row[i]</td>
    }
    else
    {
        int monthIndex = (i - fixedCols) / 4;
        <td class="month-@monthIndex">@row[i]</td>
    }
}



<style>


    th, td {
        vertical-align: middle;
    }
     body {
        background-color: #f4f6f9;
    }

    .modern-table thead th {
        font-size: 13px;
        text-transform: uppercase;
        letter-spacing: 0.03em;
        color: #6c757d;
        border-bottom: 1px solid #e5e7eb;
    }

    .modern-table tbody tr {
        border-bottom: 1px solid #f1f1f1;
    }

    .modern-table tbody tr:hover {
        background-color: #f9fafb;
    }

    .card {
        border-radius: 14px;
    }

    .btn {
        border-radius: 20px;
        padding: 4px 14px;
    }

    .form-control, .form-select {
        border-radius: 10px;
    }
    .card-body{
        font-size:13px;
    }

     .table-scroll {
        max-height: 70vh; 
        overflow-x: auto;
        border-radius: 8px;
    }

    .modern-table thead th {
        position: sticky;
        top: 0;
        background: #1c1b36;
        color:#ffffff;
        z-index: 2;
        font-size: 12px;
        text-transform: uppercase;
        letter-spacing: .02em;
    }

    .modern-table td {
        font-size: 13px;
        vertical-align: middle;
    }

    .modern-table tbody tr:hover {
        background-color: #f9fafb;
    }

    th, td {
    padding: 6px 8px;
    border: 1px solid #ddd;
    font-size: 12px;
}
.table-wrapper{
    max-width:1300px;
}

  
</style>
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
