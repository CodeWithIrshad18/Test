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


.table-scroll {
    max-height: 60vh;
    overflow: auto;
    position: relative;
}

/* Freeze header */
.freeze-table thead th {
    position: sticky;
    top: 0;
    z-index: 5;
    background: #1c1b36;
    color: white;
}

/* Freeze first 9 columns */
.freeze-table th:nth-child(1),
.freeze-table td:nth-child(1) {
    position: sticky;
    left: 0;
    background: #fff;
    z-index: 4;
}

.freeze-table th:nth-child(2),
.freeze-table td:nth-child(2) {
    position: sticky;
    left: 120px;
    background: #fff;
    z-index: 4;
}

.freeze-table th:nth-child(3),
.freeze-table td:nth-child(3) {
    position: sticky;
    left: 240px;
    background: #fff;
    z-index: 4;
}

.freeze-table th:nth-child(4),
.freeze-table td:nth-child(4) {
    position: sticky;
    left: 360px;
    background: #fff;
    z-index: 4;
}

.freeze-table th:nth-child(5),
.freeze-table td:nth-child(5) {
    position: sticky;
    left: 480px;
    background: #fff;
    z-index: 4;
}

.freeze-table th:nth-child(6),
.freeze-table td:nth-child(6) {
    position: sticky;
    left: 600px;
    background: #fff;
    z-index: 4;
}

.freeze-table th:nth-child(7),
.freeze-table td:nth-child(7) {
    position: sticky;
    left: 720px;
    background: #fff;
    z-index: 4;
}

.freeze-table th:nth-child(8),
.freeze-table td:nth-child(8) {
    position: sticky;
    left: 840px;
    background: #fff;
    z-index: 4;
}

.freeze-table th:nth-child(9),
.freeze-table td:nth-child(9) {
    position: sticky;
    left: 960px;
    background: #fff;
    z-index: 4;
}

/* Border shadow for frozen section */
.freeze-table td:nth-child(9),
.freeze-table th:nth-child(9) {
    box-shadow: 3px 0 5px rgba(0,0,0,0.1);
}

.freeze-table th, 
.freeze-table td {
    white-space: nowrap;
    min-width: 120px;
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
    <i class="fas fa-file"></i> Excel
</a>

            </div>
<div style="overflow-x:auto;">
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
