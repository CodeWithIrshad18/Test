<div class="table-wrapper">
    <div class="table-scroll">
        <table class="table table-bordered table-sm text-center modern-table freeze-table">
            ...
        </table>
    </div>
</div>

/* Wrapper respects sidebar layout */
.table-wrapper {
    position: relative;
    width: 100%;
    max-width: 100%;
}

/* THIS controls how much table is visible */
.table-scroll {
    position: relative;
    max-width: calc(100vw - 260px); /* sidebar width */
    max-height: 65vh;
    overflow: auto;
    border-radius: 10px;
}

/* Table should be wider than container */
.freeze-table {
    width: max-content;
    table-layout: fixed;
    border-collapse: separate;
    border-spacing: 0;
}

/* Cells */
.freeze-table th,
.freeze-table td {
    min-width: 140px;
    white-space: nowrap;
    background: #fff;
}

/* Sticky header */
.freeze-table thead th {
    position: sticky;
    top: 0;
    background: #1c1b36;
    color: #fff;
    z-index: 50;
}

/* ===== Freeze first 7 columns ===== */
.freeze-table th:nth-child(1),
.freeze-table td:nth-child(1) { left: 0; position: sticky; z-index: 40; }

.freeze-table th:nth-child(2),
.freeze-table td:nth-child(2) { left: 140px; position: sticky; z-index: 40; }

.freeze-table th:nth-child(3),
.freeze-table td:nth-child(3) { left: 280px; position: sticky; z-index: 40; }

.freeze-table th:nth-child(4),
.freeze-table td:nth-child(4) { left: 420px; position: sticky; z-index: 40; }

.freeze-table th:nth-child(5),
.freeze-table td:nth-child(5) { left: 560px; position: sticky; z-index: 40; }

.freeze-table th:nth-child(6),
.freeze-table td:nth-child(6) { left: 700px; position: sticky; z-index: 40; }

.freeze-table th:nth-child(7),
.freeze-table td:nth-child(7) {
    left: 840px;
    position: sticky;
    z-index: 40;
    box-shadow: 4px 0 6px rgba(0,0,0,.2);
}

/* Background for frozen columns */
.freeze-table th:nth-child(-n+7),
.freeze-table td:nth-child(-n+7) {
    background: #f8f9fa;
}

/* Header above frozen columns */
.freeze-table thead th:nth-child(-n+7) {
    z-index: 60;
}
.table-scroll {
    max-width: 100%;
}







<div class="table-wrapper">
    <div class="table-scroll">
        <table class="table table-bordered table-sm text-center modern-table freeze-table">
            ...
        </table>
    </div>
</div>

.card-body,
.container-fluid,
.main-content,
.content-wrapper {
    overflow: visible !important;
}

.table-wrapper {
    position: relative;
    width: 100%;
}

.table-scroll {
    max-height: 65vh;
    overflow: auto;
    position: relative;
}

/* Table */
.freeze-table {
    border-collapse: separate;
    border-spacing: 0;
    width: max-content;
}

/* Cells */
.freeze-table th,
.freeze-table td {
    min-width: 140px;
    white-space: nowrap;
    background: #fff;
}

/* Header */
.freeze-table thead th {
    position: sticky;
    top: 0;
    background: #1c1b36;
    color: #fff;
    z-index: 50;
}

/* ===== Freeze first 7 columns ===== */
.freeze-table th:nth-child(1),
.freeze-table td:nth-child(1) { left: 0; position: sticky; z-index: 40; }

.freeze-table th:nth-child(2),
.freeze-table td:nth-child(2) { left: 140px; position: sticky; z-index: 40; }

.freeze-table th:nth-child(3),
.freeze-table td:nth-child(3) { left: 280px; position: sticky; z-index: 40; }

.freeze-table th:nth-child(4),
.freeze-table td:nth-child(4) { left: 420px; position: sticky; z-index: 40; }

.freeze-table th:nth-child(5),
.freeze-table td:nth-child(5) { left: 560px; position: sticky; z-index: 40; }

.freeze-table th:nth-child(6),
.freeze-table td:nth-child(6) { left: 700px; position: sticky; z-index: 40; }

.freeze-table th:nth-child(7),
.freeze-table td:nth-child(7) {
    left: 840px;
    position: sticky;
    z-index: 40;
    box-shadow: 4px 0 6px rgba(0,0,0,.2);
}

/* Background for frozen cols */
.freeze-table th:nth-child(-n+7),
.freeze-table td:nth-child(-n+7) {
    background: #f8f9fa;
}

/* Header above frozen */
.freeze-table thead th:nth-child(-n+7) {
    z-index: 60;
}



<div class="table-scroll">
    <table class="table table-bordered table-sm text-center modern-table freeze-table">
        <thead>
            <tr>
                @for (int i = 0; i < fixedCols; i++)
                {
                    <th rowspan="2">@Model.Columns[i].ColumnName</th>
                }

                @foreach (var m in months)
                {
                    <th colspan="4">@m</th>
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

/* Scroll container */
.table-scroll {
    max-height: 65vh;
    overflow: auto;
    position: relative;
    border-radius: 10px;
}

/* Table layout */
.freeze-table {
    table-layout: fixed;
    width: max-content;
}

/* Common cells */
.freeze-table th,
.freeze-table td {
    min-width: 140px;
    white-space: nowrap;
    background: #fff;
}

/* Sticky header */
.freeze-table thead th {
    position: sticky;
    top: 0;
    background: #1c1b36;
    color: #fff;
    z-index: 30;
}

/* ===== FREEZE FIRST 7 COLUMNS ===== */
.freeze-table th:nth-child(1),
.freeze-table td:nth-child(1) {
    position: sticky;
    left: 0;
    z-index: 20;
}

.freeze-table th:nth-child(2),
.freeze-table td:nth-child(2) {
    position: sticky;
    left: 140px;
    z-index: 20;
}

.freeze-table th:nth-child(3),
.freeze-table td:nth-child(3) {
    position: sticky;
    left: 280px;
    z-index: 20;
}

.freeze-table th:nth-child(4),
.freeze-table td:nth-child(4) {
    position: sticky;
    left: 420px;
    z-index: 20;
}

.freeze-table th:nth-child(5),
.freeze-table td:nth-child(5) {
    position: sticky;
    left: 560px;
    z-index: 20;
}

.freeze-table th:nth-child(6),
.freeze-table td:nth-child(6) {
    position: sticky;
    left: 700px;
    z-index: 20;
}

.freeze-table th:nth-child(7),
.freeze-table td:nth-child(7) {
    position: sticky;
    left: 840px;
    z-index: 20;
    box-shadow: 4px 0 6px rgba(0,0,0,.15);
}

/* Light background for frozen columns */
.freeze-table th:nth-child(-n+7),
.freeze-table td:nth-child(-n+7) {
    background: #f8f9fa;
}

/* Header on top of frozen columns */
.freeze-table thead th:nth-child(-n+7) {
    z-index: 40;
}





.table-scroll {
    max-height: 60vh;
    overflow: auto;
    position: relative;
}

/* Fix table layout */
.freeze-table {
    table-layout: fixed;
    width: max-content;
}

/* Sticky header */
.freeze-table thead th {
    position: sticky;
    top: 0;
    background: #1c1b36;
    color: white;
    z-index: 10;
}

/* Common cell style */
.freeze-table th,
.freeze-table td {
    white-space: nowrap;
    min-width: 140px;
    background: white;
}

/* Freeze first 7 columns */
.freeze-table th:nth-child(1),
.freeze-table td:nth-child(1) { left: 0; position: sticky; z-index: 9; }

.freeze-table th:nth-child(2),
.freeze-table td:nth-child(2) { left: 140px; position: sticky; z-index: 9; }

.freeze-table th:nth-child(3),
.freeze-table td:nth-child(3) { left: 280px; position: sticky; z-index: 9; }

.freeze-table th:nth-child(4),
.freeze-table td:nth-child(4) { left: 420px; position: sticky; z-index: 9; }

.freeze-table th:nth-child(5),
.freeze-table td:nth-child(5) { left: 560px; position: sticky; z-index: 9; }

.freeze-table th:nth-child(6),
.freeze-table td:nth-child(6) { left: 700px; position: sticky; z-index: 9; }

.freeze-table th:nth-child(7),
.freeze-table td:nth-child(7) { 
    left: 840px; 
    position: sticky; 
    z-index: 9;
    box-shadow: 3px 0 5px rgba(0,0,0,0.2);
}

/* Light background for frozen columns */
.freeze-table th:nth-child(-n+7),
.freeze-table td:nth-child(-n+7) {
    background: #f8f9fa;
}

<div class="table-scroll">
<table class="table table-bordered table-sm text-center modern-table freeze-table">
    <thead>
        <tr>
            @for (int i = 0; i < fixedCols; i++)
            {
                <th rowspan="2">@Model.Columns[i].ColumnName</th>
            }

            @foreach (var m in months)
            {
                <th colspan="4">@m</th>
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




i have this code 

@{
    ViewData["Title"] = "TPR Calculation Report";
    Layout = "~/Views/Shared/_Layout2.cshtml";

    int fixedCols = 7;
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
<div style="overflow-x:auto;">
<table class="table table-bordered table-sm text-center freeze-table">
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

i want my first 7 columns freeze and constant and scroll rest only month. whole report inside a container and scroll like that
