this is my button click excel download logic for sheet 1 and now i want to add sheet2 also which GetContractorWorker2 has that query and also change hardcoded emp.DOJ<='2025-06-30' to last day of selected month

protected void btnSearch_Click(object sender, EventArgs e)
 {
     BL_Compliance_MIS blobj = new BL_Compliance_MIS();
     DataSet ds_L1 = new DataSet();

     string MonthSearch = Month.SelectedValue;
     string YearSearch = Year.SelectedValue;

     ds_L1 = blobj.GetContractorWorker(MonthSearch, YearSearch);

     if (ds_L1 != null && ds_L1.Tables.Count > 0 && ds_L1.Tables[0].Rows.Count > 0)
     {


         using (XLWorkbook wb = new XLWorkbook())
         {
             var ws = wb.Worksheets.Add("Contractor Worker Mis Report");
             DataTable dt = ds_L1.Tables[0];

             
             for (int col = 0; col < dt.Columns.Count; col++)
             {
                 var headerCell = ws.Cell(1, col + 1);
                 headerCell.Value = dt.Columns[col].ColumnName;

                 
                 if (col + 1 == 1) 
                 {
                     headerCell.Style.Fill.BackgroundColor = XLColor.Salmon;
                 }
                 else if (col + 1 >= 2 && col + 1 <= 16) 
                 {
                     headerCell.Style.Fill.BackgroundColor = XLColor.BabyBlue;
                 }
                 else if (col + 1 >= 17 && col + 1 <= 27) 
                 {
                     headerCell.Style.Fill.BackgroundColor = XLColor.Lilac; 
                 }
                 else if (col + 1 >= 28 && col + 1 <= 47) 
                 {
                     headerCell.Style.Fill.BackgroundColor = XLColor.Cream;
                 }

                
                 headerCell.Style.Font.Bold = true;
                 headerCell.Style.Alignment.Horizontal = XLAlignmentHorizontalValues.Center;

                 headerCell.Style.Border.OutsideBorder = XLBorderStyleValues.Thin;
                 headerCell.Style.Border.InsideBorder = XLBorderStyleValues.Thin;
             }

            
             for (int row = 0; row < dt.Rows.Count; row++)
             {
                 for (int col = 0; col < dt.Columns.Count; col++)
                 {
                     ws.Cell(row + 2, col + 1).Value = dt.Rows[row][col].ToString();
                 }
             }

             
             for (int col = 0; col < dt.Columns.Count; col++)
             {
                 ws.Column(col + 1).AdjustToContents();
             }

             
             ws.SheetView.FreezeRows(1);

            
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


     }
     else

     {
         MyMsgBox.show(CLMS.Control.MyMsgBox.MessageType.Warning, "No Data Found !!!");
     }

 }


        public DataSet GetContractorWorker2(string MonthSearch, string YearSearch)
        {


            int month = int.Parse(MonthSearch);
            int year = int.Parse(YearSearch);

            DateTime selectedDate = new DateTime(year, month, 1);

            string selectedMonthYear = selectedDate.ToString("MMM-yy", CultureInfo.InvariantCulture);

            string strQuery =
                         $@"select distinct  '{selectedMonthYear}' as Month,,
emp.VendorCode,emp.VendorName ,
'' as [AA Vendor],
  '' as MSME,
  emp.WorkOrder as [WorkOrder No],
  'OFFLINE' as [Compliance Route],
   emp.WorkManSlNo as [Worker ID],
   emp.AadharCard as [Worker Aadhar ID],
   wd.WorkManName as [Worker Name],
   emp.Sex,
   convert(varchar,emp.DOB,103) as [Date of Birth],
   convert(varchar,emp.DOJ,103) as [Date of Joining],
   convert(varchar,emp.DOE,103) as [Date of Exit],
   DATEDIFF(YEAR,DOB,GETDATE()) as Age,
   emp.WorkManCategory as [Skill Category],
   emp.Social_Category as [Worker AA Category],
   emp.LabourState as [Address State],
    '' as [Health Check Date],
                              '' as [Gatepass Issue Date],
                               '' as [Gatepass Renewal Due Date],
                              '' as [Division],
                              '' as [Cost center],
                             emp.DeptCode,
                             dm.DepartmentName,
                          '' as [Location],
                          '' as [Project Name],
                          '' as [Minimum wage per day],
                          '' as  [Days worked],
                          '' as  [Basic + Da],
                          '' as  [Allowances],
                          '' as  [Gross wages],
                          '' as  [pf Deduction],
                          '' as  [Esi Deduction],
                          '' as  [Other Deduction],
                          '' as [Net Wages]
from App_EmployeeMaster emp
 inner join App_WagesDetailsJharkhand wd
  on emp.AadharCard =wd.AadharNo
  and emp.VendorCode =wd.VendorCode

  inner join App_DepartmentMaster dm
  on emp.DeptCode = dm.DepartmentCode
where  emp.DOJ<='2025-06-30' and emp.AadharCard not in (select distinct AadharNo from App_WagesDetailsJharkhand 
where MonthWage='{MonthSearch}' and YearWage ='{YearSearch}') order by emp.VendorCode;";

            Dictionary<string, object> objParam = new Dictionary<string, object>();
            //objParam.Add("vendorCode", VendorCode);
            DataHelper dh = new DataHelper();
            return dh.GetDataset(strQuery, "App_Contractor_Worker_DataSet", objParam);

        }
