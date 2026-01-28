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
        max-height: 60vh; /* adjust as needed */
        overflow-y: auto;
        overflow-x: auto;
        border-radius: 8px;
    }

    .modern-table thead th {
        position: sticky;
        top: 0;
        background: #1c1b36;
        color:#ffffff;
        z-index: 2;
        font-size: 13px;
        text-transform: uppercase;
        letter-spacing: .03em;
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

  
</style>
<div class="container-fluid mt-4">

    <div class="card shadow-sm border-0">
        <div class="card-header bg-white py-3 d-flex justify-content-between align-items-center">
            <div>
                <h5 class="mb-0 fw-semibold">TPR Calculation Report</h5>
            </div>

            <div class="d-flex gap-2">
               <a asp-action="DownloadDynamicTPRReport" asp-route-kpiId="@ViewBag.KpiId" class="btn btn-outline-success btn-sm">
    <i class="bi bi-file-earmark-excel"></i> Excel
</a>

            </div>
        </div>

        <div class="card-body">


<div style="overflow-x:auto;">
<table class="table table-bordered table-sm text-center">
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
