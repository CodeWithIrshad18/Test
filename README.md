 public IActionResult DownloadDynamicTPRReport(string Dept, string FinYear)
 {
     DataTable dt = GetTPRReportData(Dept, FinYear);

     using (var wb = new XLWorkbook())
     {
         var ws = wb.Worksheets.Add("TPR Achievement");

         int fixedCols = 7; 
         int headerRow1 = 1;
         int headerRow2 = 2;
         int dataStartRow = 3;

         
         ws.Cell(1, 1).Value = "TPR Achievement";
         ws.Range(1, 1, 1, dt.Columns.Count).Merge();
         ws.Row(1).Style.Font.Bold = true;
         ws.Row(1).Style.Font.FontSize = 14;
         ws.Row(1).Style.Alignment.Horizontal = XLAlignmentHorizontalValues.Center;
         ws.Row(1).Style.Fill.BackgroundColor = XLColor.LightGray;

         
         for (int i = 0; i < fixedCols; i++)
         {
             ws.Cell(2, i + 1).Value = dt.Columns[i].ColumnName;
             ws.Range(2, i + 1, 3, i + 1).Merge();
             ws.Cell(2, i + 1).Style.Font.Bold = true;
             ws.Cell(2, i + 1).Style.Alignment.Horizontal = XLAlignmentHorizontalValues.Center;
             ws.Cell(2, i + 1).Style.Alignment.Vertical = XLAlignmentVerticalValues.Center;
             ws.Cell(2, i + 1).Style.Fill.BackgroundColor = XLColor.LightBlue;
         }

       
         List<string> months = new List<string>();

         for (int i = fixedCols; i < dt.Columns.Count; i += 4)
         {
             string month = dt.Columns[i].ColumnName.Split(' ')[0];
             months.Add(month);
         }

         int colIndex = fixedCols + 1;

         foreach (var month in months)
         {
             ws.Range(2, colIndex, 2, colIndex + 3).Merge().Value = month;
             ws.Range(2, colIndex, 2, colIndex + 3).Style.Font.Bold = true;
             ws.Range(2, colIndex, 2, colIndex + 3).Style.Alignment.Horizontal = XLAlignmentHorizontalValues.Center;
             ws.Range(2, colIndex, 2, colIndex + 3).Style.Fill.BackgroundColor = XLColor.LightSalmon;

             ws.Cell(3, colIndex).Value = "Monthly Target";
             ws.Cell(3, colIndex + 1).Value = "Actual";
             ws.Cell(3, colIndex + 2).Value = "%";
             ws.Cell(3, colIndex + 3).Value = "Actual Wt.";

             ws.Range(3, colIndex, 3, colIndex + 3).Style.Font.Bold = true;
             ws.Range(3, colIndex, 3, colIndex + 3).Style.Alignment.Horizontal = XLAlignmentHorizontalValues.Center;
             ws.Range(3, colIndex, 3, colIndex + 3).Style.Fill.BackgroundColor = XLColor.LightGoldenrodYellow;

             colIndex += 4;
         }

       
         ws.Cell(dataStartRow + 1, 1).InsertData(dt.AsEnumerable());

         int lastDataRow = dataStartRow + dt.Rows.Count;

        
         ws.Cell(lastDataRow + 1, 1).Value = "TOTAL";
         ws.Range(lastDataRow + 1, 1, lastDataRow + 1, fixedCols).Merge();
         ws.Range(lastDataRow + 1, 1, lastDataRow + 1, fixedCols).Style.Font.Bold = true;

        
         for (int c = fixedCols + 4; c <= dt.Columns.Count; c += 4)
         {
             ws.Cell(lastDataRow + 1, c).FormulaA1 =
                 $"SUM({ws.Cell(dataStartRow + 1, c).Address}:{ws.Cell(lastDataRow, c).Address})";
         }


         ws.Range(lastDataRow + 1, 1, lastDataRow + 1, dt.Columns.Count).Style.Fill.BackgroundColor = XLColor.LightGray;
         ws.Range(lastDataRow + 1, 1, lastDataRow + 1, dt.Columns.Count).Style.Font.Bold = true;

         ws.Range(1, 1, lastDataRow + 1, dt.Columns.Count).Style.Border.OutsideBorder = XLBorderStyleValues.Thin;
         ws.Range(1, 1, lastDataRow + 1, dt.Columns.Count).Style.Border.InsideBorder = XLBorderStyleValues.Thin;

         ws.Range(4, 1, lastDataRow, dt.Columns.Count).Style.Alignment.Horizontal = XLAlignmentHorizontalValues.Center;

         ws.Columns().AdjustToContents();
         ws.SheetView.FreezeRows(3);

         using (var stream = new MemoryStream())
         {
             wb.SaveAs(stream);
             stream.Position = 0;

             return File(stream.ToArray(),
                 "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",
                 "TPR_Report.xlsx");
         }
     }
 }
