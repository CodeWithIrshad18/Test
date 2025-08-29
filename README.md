this is my excel logic , i want to color the header with light blue  
            
  using (XLWorkbook wb = new XLWorkbook())
                {
                    var ws = wb.Worksheets.Add("Contractor Worker Mis Report");
                    DataTable dt = ds_L1.Tables[0];

                    for (int col = 0; col < dt.Columns.Count; col++)
                    {
                        ws.Cell(1, col + 1).Value = dt.Columns[col].ColumnName;
                    }

                    for (int row = 0; row < dt.Rows.Count; row++)
                    {

                        for (int col = 0; col < dt.Columns.Count; col++)
                        {
                            ws.Cell(row + 2, col + 1).Value = dt.Rows[row][col].ToString();
                        }



                    }

                    for (int col = 0; col <= dt.Columns.Count; col++)
                    {
                        ws.Column(col + 1).AdjustToContents();
                    }

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
