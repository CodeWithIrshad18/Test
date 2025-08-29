using (XLWorkbook wb = new XLWorkbook())
{
    var ws = wb.Worksheets.Add("Contractor Worker Mis Report");
    DataTable dt = ds_L1.Tables[0];

    // Add header row with conditional colors + borders
    for (int col = 0; col < dt.Columns.Count; col++)
    {
        var headerCell = ws.Cell(1, col + 1);
        headerCell.Value = dt.Columns[col].ColumnName;

        // Apply colors based on column index
        if (col + 1 == 1) // Column A
        {
            headerCell.Style.Fill.BackgroundColor = XLColor.Orange;
        }
        else if (col + 1 >= 2 && col + 1 <= 16) // Columns B to P
        {
            headerCell.Style.Fill.BackgroundColor = XLColor.LightBlue;
        }
        else if (col + 1 >= 18 && col + 1 <= 26) // Columns R to Z
        {
            headerCell.Style.Fill.BackgroundColor = XLColor.Plum; // Light Purple shade
        }
        else if (col + 1 >= 27 && col + 1 <= 47) // Columns AA to AU
        {
            headerCell.Style.Fill.BackgroundColor = XLColor.LightGreen;
        }

        // Common header style
        headerCell.Style.Font.Bold = true;
        headerCell.Style.Alignment.Horizontal = XLAlignmentHorizontalValues.Center;

        // Add borders
        headerCell.Style.Border.OutsideBorder = XLBorderStyleValues.Thin;
        headerCell.Style.Border.InsideBorder = XLBorderStyleValues.Thin;
    }

    // Add data rows
    for (int row = 0; row < dt.Rows.Count; row++)
    {
        for (int col = 0; col < dt.Columns.Count; col++)
        {
            ws.Cell(row + 2, col + 1).Value = dt.Rows[row][col].ToString();
        }
    }

    // Auto fit columns
    for (int col = 0; col < dt.Columns.Count; col++)
    {
        ws.Column(col + 1).AdjustToContents();
    }

    // Freeze header row
    ws.SheetView.FreezeRows(1);

    // Export to Excel
    using (MemoryStream ms = new MemoryStream())
    {
        wb.SaveAs(ms);
        Response.Clear();
        Response.Buffer = true;
        Response.ContentType = "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet";
        Response.AddHeader("Content-Disposition", "attachment; filename=Contractor Worker Mis Report.xlsx");
        Response.BinaryWrite(ms.ToArray());
        Response.Flush();
        Response.End();
    }
}




i want different colors in different column A1 will be orange, B1 to P1 light blue , R1 to Z1 light purple and AA1 to AU1 will be light green
using (XLWorkbook wb = new XLWorkbook())
{
    var ws = wb.Worksheets.Add("Contractor Worker Mis Report");
    DataTable dt = ds_L1.Tables[0];

    // Add header row with style
    for (int col = 0; col < dt.Columns.Count; col++)
    {
        var headerCell = ws.Cell(1, col + 1);
        headerCell.Value = dt.Columns[col].ColumnName;

        // Style the header
        headerCell.Style.Fill.BackgroundColor = XLColor.LightBlue; // light blue background
        headerCell.Style.Font.Bold = true; // make text bold
        headerCell.Style.Alignment.Horizontal = XLAlignmentHorizontalValues.Center; // center align
    }

    // Add data rows
    for (int row = 0; row < dt.Rows.Count; row++)
    {
        for (int col = 0; col < dt.Columns.Count; col++)
        {
            ws.Cell(row + 2, col + 1).Value = dt.Rows[row][col].ToString();
        }
    }

    // Auto fit columns
    for (int col = 0; col < dt.Columns.Count; col++)
    {
        ws.Column(col + 1).AdjustToContents();
    }

    // Freeze header row
    ws.SheetView.FreezeRows(1);

    // Export to Excel
    using (MemoryStream ms = new MemoryStream())
    {
        wb.SaveAs(ms);
        Response.Clear();
        Response.Buffer = true;
        Response.ContentType = "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet";
        Response.AddHeader("Content-Disposition", "attachment; filename=Contractor Worker Mis Report.xlsx");
        Response.BinaryWrite(ms.ToArray());
        Response.Flush();
        Response.End();
    }
}
