int month = int.Parse(MonthSearch);
int year = int.Parse(YearSearch);

// create a date with the 1st of that month
DateTime baseDate = new DateTime(year, month, 1);

// get next month/year
string nxt_mm = baseDate.AddMonths(1).ToString("MM");
string nxt_yyyy = baseDate.AddMonths(1).ToString("yyyy");

// build your reference dates
string wageDelayDate = baseDate.AddMonths(1).ToString("yyyy-MM-") + "07";
string pfEsiDelayDate = baseDate.AddMonths(1).ToString("yyyy-MM-") + "15";




public DataSet GetContractorWorker(string MonthSearch, string YearSearch)
{
    // calculate next month/year
    string nxt_mm = DateTime.ParseExact("01/" + MonthSearch + "/" + YearSearch,
                        "dd/MM/yyyy", CultureInfo.InvariantCulture)
                        .AddMonths(1).ToString("MM");

    string nxt_yyyy = DateTime.ParseExact("01/" + MonthSearch + "/" + YearSearch,
                        "dd/MM/yyyy", CultureInfo.InvariantCulture)
                        .AddMonths(1).ToString("yyyy");

    // build the base date strings for 7th and 15th of next month
    string wageDelayDate = $"{nxt_yyyy}-{nxt_mm}-07";  
    string pfEsiDelayDate = $"{nxt_yyyy}-{nxt_mm}-15";

    string strQuery = $@"
        SELECT DISTINCT
            wd.VendorCode,
            wd.WorkOrderNo,
            wd.LocationNM AS Location,
            wd.WorkManSl AS WorkerID,
            wd.AadharNo AS Worker_Aadhar,
            wd.WorkManName AS WorkerName,
            em.Sex As Gender,   
            DATEDIFF(YEAR, em.DOB, GETDATE()) AS Age,
            em.WorkManAddress AS AddressCity,
            em.LabourState AS AddressState,
            wd.WorkManCategory AS SkillCategory,
            vw.DEPT_CODE AS DepartmentCode,
            dm.DepartmentName,
            wd.BasicRate AS minWageperDay,
            wd.TotPaymentDays AS DaysWorked,
            (wd.BasicRate + wd.DARate) AS BasicDa,
            wd.OtherAllow AS Allowances,
            wd.TotalWages AS GrossWages,
            wd.PFAmt AS PfDeduct,
            wd.ESIAmt AS EsiDeduct,
            wd.OtherDeduAmt AS OtherDeduct,
            wd.NetWagesAmt AS NetWages,
            hc.HealthCheckDate,
            CONVERT(varchar, gp.CreatedOn_GP, 103) as GatePassIssueDate,
            gp.GatePassRenewalDate as GatePassRenewalDueDate,

            CASE WHEN wc.WageCompliance = 1 THEN 'Y' ELSE 'N' END AS WageCompliance,
            DATEDIFF(DAY, '{wageDelayDate}', ow.PAYMENT_DATE) AS WageDelayDays,
            CASE WHEN pf.PfCompliance = 1 THEN 'Y' ELSE 'N' END AS PfEsiCompliance,
            ps.PFChallanDate as PfComplianceDate,
            DATEDIFF(DAY, '{pfEsiDelayDate}', ps.PFChallanDate) AS PF_DelayDays,
            ps.ESIChallanDate as EsiComplianceDate,
            DATEDIFF(DAY, '{pfEsiDelayDate}', ps.ESIChallanDate) AS ESI_DelayDays

        FROM App_WagesDetailsJharkhand wd
        INNER JOIN App_Vendorwodetails vw 
            ON wd.WorkOrderNo = vw.WO_NO
        INNER JOIN App_DepartmentMaster dm 
            ON vw.DEPT_CODE = dm.DepartmentCode

        -- rest of your joins ...

        WHERE wd.MonthWage = '{MonthSearch}' 
          AND wd.YearWage = '{YearSearch}'

        ORDER BY wd.VendorCode;
    ";

    Dictionary<string, object> objParam = new Dictionary<string, object>();
    DataHelper dh = new DataHelper();
    return dh.GetDataset(strQuery, "App_Contractor_Worker_DataSet", objParam);
}




this is Months and years 
string nxt_mm = DateTime.ParseExact("01/" + mm + "/" + yyyy, "dd/MM/yyyy", CultureInfo.InvariantCulture).AddMonths(1).ToString("MM");

 //string nxt_yyyy = Convert.ToDateTime(mm + "/01/" + yyyy).AddMonths(1).ToString("yyyy");
 string nxt_yyyy = DateTime.ParseExact("01/" + mm + "/" + yyyy, "dd/MM/yyyy", CultureInfo.InvariantCulture).AddMonths(1).ToString("yyyy");


this is my query 

public DataSet GetContractorWorker(string MonthSearch, string YearSearch)
{

    string nxt_mm = DateTime.ParseExact("01/" + MonthSearch + "/" + YearSearch, "dd/MM/yyyy", CultureInfo.InvariantCulture).AddMonths(1).ToString("MM");

    //string nxt_yyyy = Convert.ToDateTime(mm + "/01/" + yyyy).AddMonths(1).ToString("yyyy");
    string nxt_yyyy = DateTime.ParseExact("01/" + MonthSearch + "/" + YearSearch, "dd/MM/yyyy", CultureInfo.InvariantCulture).AddMonths(1).ToString("yyyy");

    string strQuery =
                 $@"SELECT distinct
                    wd.VendorCode,
                    wd.WorkOrderNo,
                    wd.LocationNM AS Location,
                    wd.WorkManSl AS WorkerID,
                    wd.AadharNo AS Worker_Aadhar,
                    wd.WorkManName AS WorkerName,
                     em.Sex As Gender,   
                    DATEDIFF(YEAR, em.DOB, GETDATE()) AS Age,
                    em.WorkManAddress AS AddressCity,
                    em.LabourState AS AddressState,
                    wd.WorkManCategory AS SkillCategory,
                    vw.DEPT_CODE AS DepartmentCode,
                    dm.DepartmentName,
                    wd.BasicRate AS minWageperDay,
                    wd.TotPaymentDays AS DaysWorked,
                    (wd.BasicRate + wd.DARate) AS BasicDa,
                    wd.OtherAllow AS Allowances,
                    wd.TotalWages AS GrossWages,
                    wd.PFAmt AS PfDeduct,
                    wd.ESIAmt AS EsiDeduct,
                    wd.OtherDeduAmt AS OtherDeduct,
                    wd.NetWagesAmt AS NetWages,
                    hc.HealthCheckDate,
                    convert(varchar,gp.CreatedOn_GP,103) as GatePassIssueDate,
                    gp.GatePassRenewalDate as GatePassRenewalDueDate,

                    CASE WHEN wc.WageCompliance = 1 THEN 'Y' ELSE 'N' END AS WageCompliance,
                    DATEDIFF(DAY, '2025-07-07', ow.PAYMENT_DATE) AS WageDelayDays,
                    CASE WHEN pf.PfCompliance = 1 THEN 'Y' ELSE 'N' END AS PfEsiCompliance,
                    ps.PFChallanDate as PfComplianceDate,
                    DATEDIFF(DAY, '2025-07-15', ps.PFChallanDate) AS PF_DelayDays,
                    ps.ESIChallanDate as EsiComplianceDate,
                    DATEDIFF(DAY, '2025-07-15', ps.ESIChallanDate) AS ESI_DelayDays

                FROM App_WagesDetailsJharkhand wd
                INNER JOIN App_Vendorwodetails vw 
                    ON wd.WorkOrderNo = vw.WO_NO
                INNER JOIN App_DepartmentMaster dm 
                    ON vw.DEPT_CODE = dm.DepartmentCode

                OUTER APPLY (
                    SELECT TOP 1 Sex, DOB, WorkManAddress, LabourState
                    FROM App_EmployeeMaster em
                    WHERE em.VendorCode = wd.VendorCode 
                      AND em.AadharCard = wd.AadharNo
                    ORDER BY em.CreatedOn DESC
                ) em

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

                LEFT JOIN App_Online_Wages ow
                    ON ow.MonthWage = wd.MonthWage
                   AND ow.YearWage = wd.YearWage
                   AND ow.V_CODE = wd.VendorCode

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

                LEFT JOIN App_PF_ESI_Summary ps
                    ON ps.MonthWage = wd.MonthWage
                   AND ps.YearWage = wd.YearWage
                   AND ps.VendorCode = wd.VendorCode

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

                WHERE wd.MonthWage = '{MonthSearch}' 
                  AND wd.YearWage = '{YearSearch}'

                ORDER BY wd.VendorCode;
                ";

    Dictionary<string, object> objParam = new Dictionary<string, object>();
    //objParam.Add("vendorCode", VendorCode);
    DataHelper dh = new DataHelper();
    return dh.GetDataset(strQuery, "App_Contractor_Worker_DataSet", objParam);

}

i want if the user select Month 5 and year 2025 then it should take next 1 month , please correct this , in place of hardcode DATEDIFF(DAY, '2025-07-07', ow.PAYMENT_DATE) in this 7 will be constant and in this 15 will be constant DATEDIFF(DAY, '2025-07-15', ps.PFChallanDate) AS PF_DelayDays
