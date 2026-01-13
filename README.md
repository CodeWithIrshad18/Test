private IActionResult GenerateExcel(List<KPIReportVM> data)
{
    using var workbook = new ClosedXML.Excel.XLWorkbook();
    var ws = workbook.Worksheets.Add("KPI Report");

    // Headers
    ws.Cell(1, 1).Value = "KPI Code";
    ws.Cell(1, 2).Value = "KPI Details";
    ws.Cell(1, 3).Value = "Division";
    ws.Cell(1, 4).Value = "Department";
    ws.Cell(1, 5).Value = "Section";
    ws.Cell(1, 6).Value = "Month";
    ws.Cell(1, 7).Value = "Target";
    ws.Cell(1, 8).Value = "Actual";
    ws.Cell(1, 9).Value = "YTD";

    int row = 2;

    foreach (var item in data)
    {
        ws.Cell(row, 1).Value = item.KPICode;
        ws.Cell(row, 2).Value = item.KPIDetails;
        ws.Cell(row, 3).Value = item.Division;
        ws.Cell(row, 4).Value = item.Department;
        ws.Cell(row, 5).Value = item.Section;
        ws.Cell(row, 6).Value = item.Month;
        ws.Cell(row, 7).Value = item.TargetValue;
        ws.Cell(row, 8).Value = item.ActualValue;
        ws.Cell(row, 9).Value = item.YTDValue;
        row++;
    }

    ws.Columns().AdjustToContents();

    using var stream = new MemoryStream();
    workbook.SaveAs(stream);

    return File(stream.ToArray(),
        "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",
        "KPI_Report.xlsx");
}


public class KPIReportPdf : QuestPDF.Fluent.Document
{
    private readonly List<KPIReportVM> _data;

    public KPIReportPdf(List<KPIReportVM> data)
    {
        _data = data;
    }

    public override void Compose(IDocumentContainer container)
    {
        container.Page(page =>
        {
            page.Size(PageSizes.A4.Landscape());
            page.Margin(20);

            page.Header().Text("KPI Performance Report")
                .FontSize(18)
                .Bold()
                .AlignCenter();

            page.Content().Table(table =>
            {
                table.ColumnsDefinition(columns =>
                {
                    for (int i = 0; i < 9; i++)
                        columns.RelativeColumn();
                });

                table.Header(header =>
                {
                    string[] headers = { "KPI", "Details", "Division", "Department", "Section", "Month", "Target", "Actual", "YTD" };

                    foreach (var h in headers)
                        header.Cell().Background("#F1F3F5").Padding(5).Text(h).Bold();
                });

                foreach (var item in _data)
                {
                    table.Cell().Padding(4).Text(item.KPICode);
                    table.Cell().Padding(4).Text(item.KPIDetails);
                    table.Cell().Padding(4).Text(item.Division);
                    table.Cell().Padding(4).Text(item.Department);
                    table.Cell().Padding(4).Text(item.Section);
                    table.Cell().Padding(4).Text(item.Month);
                    table.Cell().Padding(4).Text(item.TargetValue?.ToString());
                    table.Cell().Padding(4).Text(item.ActualValue?.ToString());
                    table.Cell().Padding(4).Text(item.YTDValue?.ToString());
                }
            });

            page.Footer().AlignCenter().Text(x =>
            {
                x.Span("Generated on ");
                x.Span(DateTime.Now.ToString("dd-MMM-yyyy"));
            });
        });
    }
}

private IActionResult GeneratePdf(List<KPIReportVM> data)
{
    var doc = new KPIReportPdf(data);
    byte[] pdf = doc.GeneratePdf();

    return File(pdf, "application/pdf", "KPI_Report.pdf");
}




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
