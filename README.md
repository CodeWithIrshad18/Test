protected void SendMail(DataTable DT)
{
    string EName = Session["EName"].ToString();
    MailMessage message = new MailMessage();
    message.From = new MailAddress(Session["Email"].ToString());
    message.To.Add("sangfaislam@xyz.com");   // <-- apna email
    message.Subject = "Material Rate Changed";
    message.IsBodyHtml = true;

    StringBuilder sb = new StringBuilder();
    sb.Append("<html><body>");
    sb.Append("<h3>Material Rate has been changed by: " + EName + "</h3>");
    sb.Append("<p>Details Are Given Below:</p>");
    sb.Append("<table border='1' cellspacing='0' cellpadding='5' style='border-collapse:collapse;width:100%;'>");

    // Table Header
    sb.Append("<tr style='background:#f2f2f2;'>");
    sb.Append("<th>Material</th>");
    sb.Append("<th>Description</th>");
    sb.Append("<th>Location</th>");
    sb.Append("<th>Unit</th>");
    sb.Append("<th>Old Rate</th>");
    sb.Append("<th>New Rate</th>");
    sb.Append("</tr>");

    // Table Rows
    foreach (DataRow row in DT.Rows)
    {
        sb.Append("<tr>");
        sb.Append("<td>" + row["Material"].ToString() + "</td>");
        sb.Append("<td>" + row["Description"].ToString() + "</td>");
        sb.Append("<td>" + row["Location"].ToString() + "</td>");
        sb.Append("<td>" + row["Unit"].ToString() + "</td>");
        sb.Append("<td>" + row["Old Rate"].ToString() + "</td>");
        sb.Append("<td>" + row["New Rate"].ToString() + "</td>");
        sb.Append("</tr>");
    }

    sb.Append("</table>");
    sb.Append("</body></html>");

    message.Body = sb.ToString();

    try
    {
        var smtp = new System.Net.Mail.SmtpClient("10.101.11.255");
        smtp.Port = 25;
        smtp.Timeout = 20000;
        smtp.Send(message);
    }
    catch (Exception ex)
    {
        throw ex;
    }
}



;WITH LatestEmployee AS (
    SELECT VendorCode, AadharCard, Sex, DOB, WorkManAddress, LabourState,
           ROW_NUMBER() OVER (PARTITION BY VendorCode, AadharCard ORDER BY CreatedOn DESC) AS rn
    FROM App_EmployeeMaster
),
LatestGatePass AS (
    SELECT v_code, Addhar, wo_no_gp, 
           CASE WHEN Medical_status='FIT' 
                THEN CONVERT(varchar, ApprovedOn_IISM,103) 
                ELSE 'UNFIT/Rejected' END AS HealthCheckDate,
           CreatedOn_GP,
           DATEDIFF(day, GatePass_Validity, GETDATE()) AS GatePassRenewalDate,
           ROW_NUMBER() OVER (PARTITION BY v_code, Addhar, wo_no_gp ORDER BY CreatedOn_GP DESC) AS rn
    FROM App_Online_Gatepass_Details
)
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

    em.Sex,
    DATEDIFF(YEAR, em.DOB, GETDATE()) AS Age,
    em.WorkManAddress,
    em.LabourState,

    CASE WHEN owd.WorkManSl IS NOT NULL THEN 'Y' ELSE 'N' END AS WageCompliance,
    DATEDIFF(DAY, '2025-07-07', ow.PAYMENT_DATE) AS WageDelayDays,

    CASE WHEN pd.WorkManSl IS NOT NULL THEN 'Y' ELSE 'N' END AS PfCompliance,
    ps.PFChallanDate,
    DATEDIFF(DAY, '2025-07-15', ps.PFChallanDate) AS PF_DelayDays,
    ps.ESIChallanDate,
    DATEDIFF(DAY, '2025-07-15', ps.ESIChallanDate) AS ESI_DelayDays,

    gp.HealthCheckDate,
    gp.CreatedOn_GP,
    gp.GatePassRenewalDate

FROM App_WagesDetailsJharkhand wd
INNER JOIN App_Vendorwodetails vw ON wd.WorkOrderNo = vw.WO_NO
INNER JOIN App_DepartmentMaster dm ON vw.DEPT_CODE = dm.DepartmentCode

LEFT JOIN LatestEmployee em 
       ON em.VendorCode = wd.VendorCode AND em.AadharCard = wd.AadharNo AND em.rn=1
LEFT JOIN App_Online_Wages_Details owd 
       ON owd.MonthWage=wd.MonthWage AND owd.YearWage=wd.YearWage 
      AND owd.WorkOrderNo=wd.WorkOrderNo AND owd.VendorCode=wd.VendorCode
      AND owd.WorkManSl=wd.WorkManSl AND owd.AadharNo=wd.AadharNo
LEFT JOIN App_Online_Wages ow 
       ON ow.MonthWage=wd.MonthWage AND ow.YearWage=wd.YearWage 
      AND ow.V_CODE=wd.VendorCode
LEFT JOIN App_PF_ESI_Details pd 
       ON pd.MonthWage=wd.MonthWage AND pd.YearWage=wd.YearWage 
      AND pd.WorkOrderNo=wd.WorkOrderNo AND pd.VendorCode=wd.VendorCode
      AND pd.WorkManSl=wd.WorkManSl AND pd.AadharNo=wd.AadharNo
LEFT JOIN App_PF_ESI_Summary ps 
       ON ps.MonthWage=wd.MonthWage AND ps.YearWage=wd.YearWage 
      AND ps.VendorCode=wd.VendorCode
LEFT JOIN LatestGatePass gp 
       ON gp.v_code=wd.VendorCode AND gp.Addhar=wd.AadharNo 
      AND gp.wo_no_gp=wd.WorkOrderNo AND gp.rn=1

WHERE wd.MonthWage='06' AND wd.YearWage='2025'
ORDER BY wd.VendorCode;




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
    CASE WHEN wc.WageCompliance = 1 THEN 'Y' ELSE 'N' END AS WageCompliance,

    -- Wage Delay Days
    DATEDIFF(DAY, '2025-07-07', ow.PAYMENT_DATE) AS WageDelayDays,

    -- PF/ESI Compliance
    CASE WHEN pf.PfCompliance = 1 THEN 'Y' ELSE 'N' END AS PfCompliance,

    -- PF Challan
    ps.PFChallanDate,
    DATEDIFF(DAY, '2025-07-15', ps.PFChallanDate) AS PF_DelayDays,

    -- ESI Challan
    ps.ESIChallanDate,
    DATEDIFF(DAY, '2025-07-15', ps.ESIChallanDate) AS ESI_DelayDays,

    -- Health Check
    hc.HealthCheckDate,

    -- GatePass
    gp.CreatedOn_GP,
    gp.GatePassRenewalDate

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

-- Health Check (latest record)
OUTER APPLY (
    SELECT TOP 1 
        CASE 
            WHEN g.Medical_status = 'FIT' 
            THEN CONVERT(varchar, g.ApprovedOn_IISM, 103) 
            ELSE 'UNFIT/Rejected' 
        END AS HealthCheckDate
    FROM App_Online_Gatepass_Details g
    WHERE g.Addhar = wd.AadharNo 
      AND g.v_code = wd.VendorCode
    ORDER BY g.CreatedOn DESC
) hc

-- GatePass Renewal (latest record)
OUTER APPLY (
    SELECT TOP 1 
        g.CreatedOn_GP,
        DATEDIFF(DAY, g.GatePass_Validity, GETDATE()) AS GatePassRenewalDate
    FROM App_Online_Gatepass_Details g
    WHERE g.Addhar = wd.AadharNo
      AND g.v_code = wd.VendorCode
      AND g.wo_no_gp = wd.WorkOrderNo
    ORDER BY g.CreatedOn_GP DESC
) gp

WHERE wd.MonthWage = '06' 
  AND wd.YearWage = '2025'

ORDER BY wd.VendorCode;



now add these 2 query also


  select top 1 case when Medical_status='FIT' then convert(varchar,ApprovedOn_IISM,103) else 'UNFIT/Rejected' end as HealthCheckDate from App_Online_Gatepass_Details where Addhar ='929894660928' and v_code ='10038'  
 order by CreatedOn desc

 select top 1 CreatedOn_GP,DATEDIFF(day,GatePass_Validity,GETDATE()) GatePassRenewalDate from App_Online_Gatepass_Details where Addhar ='929894660928' and v_code ='10038'
 and wo_no_gp ='4700024126' order by CreatedOn_GP desc
