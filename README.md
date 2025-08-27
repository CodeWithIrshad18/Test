 select PFChallanDate,
 (select DATEDIFF(Day,'2025-07-15',PFChallanDate) as PF_DelayDays from App_PF_ESI_Summary where  Monthwage ='06' and YearWage='2025' and VendorCode ='10517') from App_PF_ESI_Summary

  select ESIChallanDate,
 (select DATEDIFF(Day,'2025-07-15',ESIChallanDate) as ESi_DelayDays from App_PF_ESI_Summary where  Monthwage ='06' and YearWage='2025' and VendorCode ='10517') from App_PF_ESI_Summary
