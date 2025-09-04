DateTime firstDayOfMonth = new DateTime(year, month, 1);
DateTime lastDayOfMonth = new DateTime(year, month, DateTime.DaysInMonth(year, month));

string firstDay = firstDayOfMonth.ToString("yyyy-MM-dd");
string lastDay = lastDayOfMonth.ToString("yyyy-MM-dd");
where emp.DOJ <= '{lastDay}'
  and (emp.DOE IS NULL OR emp.DOE > '{firstDay}')
  and emp.AadharCard not in (
       select distinct AadharNo 
       from App_WagesDetailsJharkhand 
       where MonthWage='{MonthSearch}' and YearWage='{YearSearch}'
  )

        
        
        
        public DataSet GetContractorWorker2(string MonthSearch, string YearSearch)
         {
            int month = int.Parse(MonthSearch);
            int year = int.Parse(YearSearch);

            DateTime selectedDate = new DateTime(year, month, 1);
            DateTime lastDayOfMonth = new DateTime(year, month, DateTime.DaysInMonth(year, month));

            string selectedMonthYear = selectedDate.ToString("MMM-yy", CultureInfo.InvariantCulture);
            string lastDay = lastDayOfMonth.ToString("yyyy-MM-dd");

            string strQuery =
                $@"select distinct '{selectedMonthYear}' as Month,
emp.VendorName,
emp.VendorCode,
'' as [AA Vendor],
'' as MSME,
emp.WorkOrder as [WorkOrder No],
'OFFLINE' as [Compliance Route],
'' as [Wage Payment date],
  '' as [Wage Compliance],
  '' as [Wage Delay days],
  '' as [Pf Compliance Date],
  '' as [Pf/Esi Compliance],
  '' as [PF Delay Days],
  '' as [Esi Compliance Date],
  '' as [ESI Delay Days],
  '' as [Vendor Consequence Management],
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
'' as [Days worked],
'' as [Basic + Da],
'' as [Allowances],
'' as [Gross wages],
'' as [pf Deduction],
'' as [Esi Deduction],
'' as [Other Deduction],
'' as [Net Wages]
from App_EmployeeMaster emp
inner join App_WagesDetailsJharkhand wd
    on emp.AadharCard = wd.AadharNo
   and emp.VendorCode = wd.VendorCode
inner join App_DepartmentMaster dm
    on emp.DeptCode = dm.DepartmentCode
where emp.DOJ <= '{lastDay}'
  and emp.AadharCard not in (
       select distinct AadharNo 
       from App_WagesDetailsJharkhand 
       where MonthWage='{MonthSearch}' and YearWage='{YearSearch}'
  )
order by emp.VendorCode;";

            Dictionary<string, object> objParam = new Dictionary<string, object>();
            DataHelper dh = new DataHelper();
            return dh.GetDataset(strQuery, "App_Contractor_Worker_DataSet", objParam);
        }
