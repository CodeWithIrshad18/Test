// Apply borders to GROUP row
ws.Range(groupRow, 1, groupRow, dt.Columns.Count)
  .Style.Border.OutsideBorder = XLBorderStyleValues.Thin;

ws.Range(groupRow, 1, groupRow, dt.Columns.Count)
  .Style.Border.InsideBorder = XLBorderStyleValues.Thin;


// Apply borders to PAYOUT row
ws.Range(payoutRow, 1, payoutRow, dt.Columns.Count)
  .Style.Border.OutsideBorder = XLBorderStyleValues.Thin;

ws.Range(payoutRow, 1, payoutRow, dt.Columns.Count)
  .Style.Border.InsideBorder = XLBorderStyleValues.Thin;

ws.Range(groupRow, 1, groupRow, dt.Columns.Count).Style.Fill.BackgroundColor = XLColor.LightGray;
ws.Range(groupRow, 1, groupRow, dt.Columns.Count).Style.Font.Bold = true;

ws.Range(payoutRow, 1, payoutRow, dt.Columns.Count).Style.Fill.BackgroundColor = XLColor.LightGray;
ws.Range(payoutRow, 1, payoutRow, dt.Columns.Count).Style.Font.Bold = true;





Cannot implicitly convert type 'object' to 'ClosedXML.Excel.XLCellValue'. An explicit conversion exists (are you missing a cast?)
Cannot implicitly convert type 'object' to 'ClosedXML.Excel.XLCellValue'. An explicit conversion exists (are you missing a cast?)
Cannot implicitly convert type 'object' to 'ClosedXML.Excel.XLCellValue'. An explicit conversion exists (are you missing a cast?)
