        public DataSet GetContractorWorker(string MonthSearch, string YearSearch)
        {


            int month = int.Parse(MonthSearch);
            int year = int.Parse(YearSearch);

           
            DateTime baseDate = new DateTime(year, month, 1);

          
            string nxt_mm = baseDate.AddMonths(1).ToString("MM");
            string nxt_yyyy = baseDate.AddMonths(1).ToString("yyyy");

           
            string wageDelayDate = baseDate.AddMonths(1).ToString("yyyy-MM-") + "07";
            string pfEsiDelayDate = baseDate.AddMonths(1).ToString("yyyy-MM-") + "15";

            DateTime selectedDate = new DateTime(year, month, 1);

            string selectedMonthYear = selectedDate.ToString("MMM-yy", CultureInfo.InvariantCulture);

            string strQuery =
                         $@"SELECT distinct
                         '{selectedMonthYear}' as Month,
                             vg.V_NAME AS [Vendor Name],
                            wd.VendorCode [Vendor Code],
                            '' as [AA Vendor],
                            '' as MSME,
                            wd.WorkOrderNo as [WorkOrder No],
                            'ONLINE' as [Compliance Route],
                             convert(varchar,ow.PAYMENT_DATE,103) [Wage Payment Date],
                             CASE WHEN wc.WageCompliance = 1 THEN 'Y' ELSE 'WIP' END AS [Wage Compliance],
                            IIF(DATEDIFF(DAY, '{wageDelayDate}', ow.PAYMENT_DATE) < 0, 0, DATEDIFF(DAY, '{wageDelayDate}', ow.PAYMENT_DATE)) 
                            AS [Wage Delay Days],
                            convert(varchar,ps.PFChallanDate,103)  [Pf Compliance Date],
                             CASE WHEN pf.PfCompliance = 1 THEN 'Y' ELSE 'WIP' END AS [Pf/Esi Compliance],
                             IIF(DATEDIFF(DAY, '{pfEsiDelayDate}',  ps.PFChallanDate) < 0, 0, DATEDIFF(DAY, '{pfEsiDelayDate}', ps.PFChallanDate)) 
                            AS [PF Delay Days],
                              convert(varchar,ps.ESIChallanDate,103) [Esi Compliance Date],
                              IIF(DATEDIFF(DAY, '{pfEsiDelayDate}', ps.ESIChallanDate) < 0, 0, DATEDIFF(DAY, '{pfEsiDelayDate}', ps.ESIChallanDate))
 AS [ESI Delay Days],
                              '' as [Vendor Consequence Management],

                            wd.WorkManSl AS [Worker ID],
                            wd.AadharNo AS [Worker Aadhar ID],
                            wd.WorkManName AS [Worker Name],
                             em.Sex As [Gender], 
                             convert(varchar,em.DOB,103) AS [Date of Birth],
                             convert(varchar,em.DOJ,103) AS [Date of Joining],
                             convert(varchar,em.DOE,103) AS [Date of Exit],
                            DATEDIFF(YEAR, em.DOB, GETDATE()) AS Age,
                             wd.WorkManCategory AS [Skill Category],
                             em.Social_Category AS [Worker AA Category],
                             em.Religion AS [Religion],
                            em.LabourState AS [Address State],
                             hc.HealthCheckDate as [Health Check Date],
                             gi.GatepassIssueDate,
                              convert(varchar,gp.GatepassValidity,103) as [Gatepass Validity],
                              '' as [Division],
                              '' as [Cost Center],
                            vw.DEPT_CODE AS [Department Code],
                            dm.DepartmentName AS [Department Name],
                            wd.LocationNM AS [Location],
                            '' as [Project Name],
                            wd.BasicRate AS [Minimum Wage per Day],
                            wd.TotPaymentDays AS [Days Worked],
                            (wd.BasicRate + wd.DARate) AS [Basic+Da],
                            wd.OtherAllow AS [Allowances],
                            wd.TotalWages AS [Gross Wages],
                            wd.PFAmt AS [Pf Deduction],
                            wd.ESIAmt AS [Esi Deduction],
                            wd.OtherDeduAmt AS [Other Deduction],
                            wd.NetWagesAmt AS [Net Wages]

                        FROM App_WagesDetailsJharkhand wd
                        INNER JOIN App_Vendorwodetails vw 
                            ON wd.WorkOrderNo = vw.WO_NO
                        INNER JOIN App_DepartmentMaster dm 
                            ON vw.DEPT_CODE = dm.DepartmentCode
                              LEFT JOIN App_Vendor_Reg vg
                            ON wd.VendorCode = vg.V_CODE

                        OUTER APPLY (
                            SELECT TOP 1 Sex, DOB, WorkManAddress, LabourState, Social_Category, DOE, doj,Religion
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
CONVERT(varchar, g.CreatedOn_GP, 103) AS [GatepassIssueDate]
    FROM App_Online_Gatepass_Details g
    WHERE g.Addhar = wd.AadharNo 
      AND g.v_code = wd.VendorCode
    ORDER BY g.CreatedOn DESC
) gi


                        OUTER APPLY (
                            SELECT TOP 1 
                               g.GatePass_Validity AS GatepassValidity
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
