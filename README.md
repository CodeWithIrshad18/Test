<style>
    .table-scroll {
        max-height: 60vh; /* adjust as needed */
        overflow-y: auto;
        overflow-x: auto;
        border-radius: 8px;
    }

    .modern-table thead th {
        position: sticky;
        top: 0;
        background: #f8f9fa;
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
</style>






<div class="container-fluid mt-4">

    <div class="card shadow-sm border-0">
        <div class="card-header bg-white py-3 d-flex justify-content-between align-items-center">
            <div>
                <h5 class="mb-0 fw-semibold">KPI Performance Report</h5>
            </div>

            <div class="d-flex gap-2">
                <a asp-action="ExportExcel" class="btn btn-outline-success btn-sm">
                    <i class="bi bi-file-earmark-excel"></i> Excel
                </a>
                <a asp-action="ExportPdf" class="btn btn-outline-danger btn-sm">
                    <i class="bi bi-file-earmark-pdf"></i> PDF
                </a>
            </div>
        </div>

        <div class="card-body">

            <!-- Filters -->
            <div class="row g-2 mb-3">
                <div class="col-md-3">
                    <input type="text" id="searchBox" class="form-control form-control-sm" placeholder="Search KPI...">
                </div>
                <div class="col-md-2">
                    <select class="form-select form-select-sm">
                        <option>All Divisions</option>
                    </select>
                </div>
                <div class="col-md-2">
                    <select class="form-select form-select-sm">
                        <option>All Months</option>
                    </select>
                </div>
            </div>

            <!-- Table -->
            <div class="table-responsive">
                <table class="table table-hover align-middle modern-table" id="kpiTable">
                    <thead>
                        <tr>
                            <th>KPI Code</th>
                            <th>Department</th>
                            <th>Division</th>
                            <th>Section</th>
                            <th>Month</th>
                            <th>Target</th>
                            <th>Actual</th>
                            <th>YTD</th>
                        </tr>
                    </thead>
                    <tbody>
                        @foreach (var item in Model)
                        {
                            <tr>
                                <td>
                                    <div class="fw-semibold">@item.KPICode</div>
                                    <small class="text-muted">@item.KPIDetails</small>
                                </td>
                                <td>@item.Division</td>
                                <td>@item.Department</td>
                                <td>@item.Section</td>
                                <td>@item.Month</td>
                                <td>@item.TargetValue</td>
                                <td>@item.ActualValue</td>
                                <td>@item.YTDValue</td>
                            </tr>
                        }
                    </tbody>
                </table>
            </div>

        </div>
    </div>
</div>
