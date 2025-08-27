SELECT 
    wd.VendorCode,
    wd.WorkOrderNo,
    wd.LocationNM AS Location,
    wd.WorkManSl AS WorkerID,
    wd.AadharNo AS Worker_Aadhar,
    wd.WorkManName AS WorkerName,
    wd.WorkManCategory AS SkillCategory,
    vw.DEPT_CODE AS DepartmentCode,
    dm.DepartmentName,
    wd.BasicRate,
    wd.TotPaymentDays AS DaysWorked,
    (wd.BasicRate + wd.DARate) AS BasicDa,
    wd.OtherAllow,
    wd.TotalWages,
    wd.PFAmt,
    wd.ESIAmt,
    wd.OtherDeduAmt,
    wd.NetWagesAmt,

    -- Employee Master Info
    em.Sex,
    DATEDIFF(YEAR, em.DOB, GETDATE()) AS Age,
    em.WorkManAddress,
    em.LabourState,

    -- Wage Compliance
    CASE 
        WHEN wc.WageCompliance = 1 THEN 'Y' ELSE 'N' 
    END AS WageCompliance,

    -- Wage Delay Days
    DATEDIFF(DAY, '2025-07-07', ow.PAYMENT_DATE) AS WageDelayDays,

    -- PF/ESI Compliance
    CASE 
        WHEN pf.PfCompliance = 1 THEN 'Y' ELSE 'N' 
    END AS PfCompliance,

    -- PF Challan
    ps.PFChallanDate,
    DATEDIFF(DAY, '2025-07-15', ps.PFChallanDate) AS PF_DelayDays,

    -- ESI Challan
    ps.ESIChallanDate,
    DATEDIFF(DAY, '2025-07-15', ps.ESIChallanDate) AS ESI_DelayDays

FROM App_WagesDetailsJharkhand wd
INNER JOIN App_Vendorwodetails vw 
    ON wd.WorkOrderNo = vw.WO_NO
INNER JOIN App_DepartmentMaster dm 
    ON vw.DEPT_CODE = dm.DepartmentCode

-- Employee Master (pick latest by CreatedOn)
OUTER APPLY (
    SELECT TOP 1 Sex, DOB, WorkManAddress, LabourState
    FROM App_EmployeeMaster em
    WHERE em.VendorCode = wd.VendorCode 
      AND em.AadharCard = wd.AadharNo
    ORDER BY em.CreatedOn DESC
) em

-- Wage Compliance
OUTER APPLY (
    SELECT 1 AS WageCompliance
    FROM App_Online_Wages_Details owd
    WHERE owd.MonthWage = wd.MonthWage
      AND owd.YearWage = wd.YearWage
      AND owd.WorkOrderNo = wd.WorkOrderNo
      AND owd.VendorCode = wd.VendorCode
      AND owd.WorkManSl = wd.WorkManSl
      AND owd.AadharNo = wd.AadharNo
) wc

-- Wage Payment Delay
LEFT JOIN App_Online_Wages ow
    ON ow.MonthWage = wd.MonthWage
   AND ow.YearWage = wd.YearWage
   AND ow.V_CODE = wd.VendorCode

-- PF/ESI Compliance
OUTER APPLY (
    SELECT 1 AS PfCompliance
    FROM App_PF_ESI_Details pd
    WHERE pd.MonthWage = wd.MonthWage
      AND pd.YearWage = wd.YearWage
      AND pd.WorkOrderNo = wd.WorkOrderNo
      AND pd.VendorCode = wd.VendorCode
      AND pd.WorkManSl = wd.WorkManSl
      AND pd.AadharNo = wd.AadharNo
) pf

-- PF/ESI Summary (for challan dates)
LEFT JOIN App_PF_ESI_Summary ps
    ON ps.MonthWage = wd.MonthWage
   AND ps.YearWage = wd.YearWage
   AND ps.VendorCode = wd.VendorCode

WHERE wd.MonthWage = '06' 
  AND wd.YearWage = '2025'

ORDER BY wd.VendorCode;

 
 
 select PFChallanDate,
 (select DATEDIFF(Day,'2025-07-15',PFChallanDate) as PF_DelayDays from App_PF_ESI_Summary where  Monthwage ='06' and YearWage='2025' and VendorCode ='10517') from App_PF_ESI_Summary

  select ESIChallanDate,
 (select DATEDIFF(Day,'2025-07-15',ESIChallanDate) as ESi_DelayDays from App_PF_ESI_Summary where  Monthwage ='06' and YearWage='2025' and VendorCode ='10517') from App_PF_ESI_Summary
