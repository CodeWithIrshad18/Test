-- Wage Generation main query 

select wd.VendorCode,wd.WorkOrderNo,wd.LocationNM as Location,wd.WorkManSl as WorkerID,wd.AadharNo as Worker_Aadhar,wd.WorkManName 
as WorkerName,wd.WorkManCategory as SkillCategory,vw.DEPT_CODE as DeparmentCode,dm.DepartmentName,wd.BasicRate,wd.TotPaymentDays as DaysWorked,(wd.BasicRate+wd.DARate) 
as BasicDa,wd.OtherAllow,wd.TotalWages,wd.PFAmt,wd.ESIAmt,
wd.OtherDeduAmt,wd.NetWagesAmt from App_WagesDetailsJharkhand wd
inner join App_Vendorwodetails as vw
on wd.WorkOrderNo = vw.WO_NO
inner join App_DepartmentMaster dm
on vw.DEPT_CODE = dm.DepartmentCode where MonthWage='06' and YearWage ='2025' order by VendorCode


--Employee Master using subquery

select top 1 Sex,Datediff(YEAR,DOB,GETDATE()) as Age,WorkManCategory,WorkManAddress,LabourState from App_EmployeeMaster 
where VendorCode ='17201' and AadharCard ='922488905994' order by CreatedOn desc

--wage compliance

select case when exists ( select * from App_Online_Wages_Details where Monthwage ='06' and YearWage='2025' and WorkOrderNo ='4700024126' and VendorCode ='10038'
 and WorkManSl='56' and AadharNo ='929894660928' ) then 'Y' else 'N' end as WageCompliance

 --wage compliance delay days
 select DATEDIFF(Day,'2025-07-07',PAYMENT_DATE) from App_Online_Wages where  Monthwage ='06' and YearWage='2025' and V_CODE =''

 ---pf/esi compliance					

 select case when exists ( select * from App_PF_ESI_Details where Monthwage ='06' and YearWage='2025' and WorkOrderNo ='4700024126' and VendorCode ='10038'
 and WorkManSl='56' and AadharNo ='929894660928' ) then 'Y' else 'N' end as PfCompliance 

  select PFChallanDate,
  (select DATEDIFF(Day,'2025-07-15',PFChallanDate) as PF_DelayDays from App_PF_ESI_Summary where  Monthwage ='06' and YearWage='2025' and VendorCode ='10517') from App_PF_ESI_Summary


  ---combine all these query into one query with one row 
