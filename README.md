WITH RankedAttendance AS (
    SELECT
        AD.VendorCode,
        AD.WorkOrderNo,
        CASE 
            WHEN EM.AadharCard IS NULL THEN 'Unknown'
            ELSE EM.Sex 
        END AS Sex,
        CASE 
            WHEN EM.AadharCard IS NULL THEN 'Unknown'
            ELSE EM.Social_Category 
        END AS Social_Category,
        AD.WorkManCategory,
        AD.AadharNo AS AadharCard,   -- keep attendance Aadhar as source of truth
        CAST(AD.Present AS INT) AS Present,
        AD.dates,
        CASE 
            WHEN EM.AadharCard IS NULL THEN 1 ELSE 0 
        END AS IsUnknown   -- 🔍 helper flag
    FROM App_AttendanceDetails AD
    OUTER APPLY (
        SELECT TOP 1 EM.Sex, EM.Social_Category, EM.AadharCard
        FROM App_EmployeeMaster EM
        WHERE EM.AadharCard = AD.AadharNo
          AND EM.VendorCode = AD.VendorCode
    ) EM
    WHERE AD.dates >= '2025-07-01'
      AND AD.dates < '2025-07-31 23:59:59'
),
AttendanceAgg AS (
    SELECT
        VendorCode,
        WorkOrderNo,
        Sex,
        Social_Category,
        WorkManCategory,
        COUNT(DISTINCT AadharCard) AS TotalWorkers,
        SUM(Present) AS TotalMandays,
        MAX(IsUnknown) AS IsUnknown   -- carry forward
    FROM RankedAttendance
    GROUP BY VendorCode, WorkOrderNo, Sex, Social_Category, WorkManCategory
),
    SUM(CASE WHEN AA.IsUnknown = 1 THEN AA.TotalWorkers ELSE 0 END) AS UNKNOWN_NO_OF_WORKERS,
    SUM(CASE WHEN AA.IsUnknown = 1 THEN AA.TotalMandays ELSE 0 END) AS UNKNOWN_MANDAYS,

                 
                 
                 SELECT 
                       newid() as ID,
                       ROW_NUMBER() OVER (ORDER BY mis.vendorcode) AS SlNo,
                       '07' AS ProcessMonth,
                       '2025' AS ProcessYear,
                       mis.vendorcode,
                       VM.V_NAME,
                       mis.workorder,
                       mis.from_date,
                       mis.to_date,
                       ISNULL(DM.DepartmentCode, 'Work Order Not Registered') AS DepartmentCode,
                       ISNULL(DM.DepartmentName, 'Work Order Not Registered') AS DepartmentName,
                       ISNULL(LM.Location, 'Work Order Not Registered') AS Location,
                       ISNULL(CC.RESPONSIBLE_PERSON, 'Vendor Registration Not Done') AS RESPONSIBLE_PERSON_OF_THE_CONTRACTOR,
                       mis.Description as NatureOfWork,

                       -- Existing Male counts
                       SUM(CASE WHEN AA.Sex = 'M' THEN AA.TotalWorkers ELSE 0 END) AS MALE_NO_OF_MALE_WORKERS,
                       SUM(CASE WHEN AA.Sex = 'M' AND AA.Social_Category IN ('ST','SC') THEN AA.TotalWorkers ELSE 0 END) AS MALE_NOS_OF_SC_ST_WORKERS,
                       SUM(CASE WHEN AA.Sex = 'M' AND AA.Social_Category = 'OBC' THEN AA.TotalWorkers ELSE 0 END) AS MALE_NOS_OF_OBC_WORKERS,
                       SUM(CASE WHEN AA.Sex = 'M' THEN AA.TotalMandays ELSE 0 END) AS MALE_MANDAYS,
                       SUM(CASE WHEN AA.Sex = 'M' AND AA.Social_Category IN ('ST','SC') THEN AA.TotalMandays ELSE 0 END) AS MALE_MANDAYS_SC_ST,
                       SUM(CASE WHEN AA.Sex = 'M' AND AA.Social_Category = 'OBC' THEN AA.TotalMandays ELSE 0 END) AS MALE_MANDAYS_OBC,

                       -- Existing Female counts
                       SUM(CASE WHEN AA.Sex = 'F' THEN AA.TotalWorkers ELSE 0 END) AS FEMALE_NO_OF_FEMALE_WORKERS,
                       SUM(CASE WHEN AA.Sex = 'F' AND AA.Social_Category IN ('ST','SC') THEN AA.TotalWorkers ELSE 0 END) AS FEMALE_NOS_OF_SC_ST_WORKERS,
                       SUM(CASE WHEN AA.Sex = 'F' AND AA.Social_Category = 'OBC' THEN AA.TotalWorkers ELSE 0 END) AS FEMALE_NOS_OF_OBC_WORKERS,
                       SUM(CASE WHEN AA.Sex = 'F' THEN AA.TotalMandays ELSE 0 END) AS FEMALE_MANDAYS,
                       SUM(CASE WHEN AA.Sex = 'F' AND AA.Social_Category IN ('ST','SC') THEN AA.TotalMandays ELSE 0 END) AS FEMALE_MANDAYS_SC_ST,
                       SUM(CASE WHEN AA.Sex = 'F' AND AA.Social_Category = 'OBC' THEN AA.TotalMandays ELSE 0 END) AS FEMALE_MANDAYS_OBC,

                       -- 🔍 New: Unknown workers block
                       SUM(CASE WHEN AA.Sex = 'Unknown' THEN AA.TotalWorkers ELSE 0 END) AS UNKNOWN_NO_OF_WORKERS,
                       SUM(CASE WHEN AA.Sex = 'Unknown' THEN AA.TotalMandays ELSE 0 END) AS UNKNOWN_MANDAYS,

                       ISNULL(SUM(AA.TotalWorkers), 0) AS Total_NOS_OF_WORKERS,
                       ISNULL(SUM(AA.TotalMandays), 0) AS Total_Mandays,




WITH RankedAttendance AS (
    SELECT
        AD.VendorCode,
        AD.WorkOrderNo,
        ISNULL(EM.Sex, 'Unknown') AS Sex,   -- fallback when no match
        ISNULL(EM.Social_Category, 'Unknown') AS Social_Category,
        AD.WorkManCategory,
        ISNULL(EM.AadharCard, AD.AadharNo) AS AadharCard, -- fallback to attendance Aadhar
        CAST(AD.Present AS INT) AS Present,
        AD.dates
    FROM App_AttendanceDetails AD
    OUTER APPLY (
        SELECT TOP 1 EM.Sex, EM.Social_Category, EM.AadharCard
        FROM App_EmployeeMaster EM
        WHERE EM.AadharCard = AD.AadharNo
          AND EM.VendorCode = AD.VendorCode
    ) EM
    WHERE AD.dates >= '2025-07-01'
      AND AD.dates < '2025-07-31 23:59:59'
),



this is my full query 
 WITH RankedAttendance AS (
    SELECT
        AD.VendorCode,
        AD.WorkOrderNo,
        ISNULL(EM.Sex, 'Unknown') AS Sex,
        ISNULL(EM.Social_Category, 'Unknown') AS Social_Category,
        AD.WorkManCategory,
        ISNULL(EM.AadharCard, AD.AadharNo) AS AadharCard,
        CAST(AD.Present AS INT) AS Present,
        AD.dates
    FROM App_AttendanceDetails AD
    OUTER APPLY (
        SELECT TOP 1 *
        FROM App_EmployeeMaster EM
        WHERE EM.AadharCard = AD.AadharNo
          AND EM.VendorCode = AD.VendorCode
          
    ) EM
    WHERE AD.dates >= '2025-07-01'
      AND AD.dates < '2025-07-31 23:59:59'
),

  AttendanceAgg AS (
    SELECT
        VendorCode,
        WorkOrderNo,
        Sex,
        Social_Category,
        WorkManCategory,
        COUNT(DISTINCT AadharCard) AS TotalWorkers,
        SUM(Present) AS TotalMandays
    FROM RankedAttendance
    GROUP BY VendorCode, WorkOrderNo, Sex, Social_Category, WorkManCategory
),
  ContractorContact AS (
                     SELECT
                         CREATEDBY AS VendorCode,
                         STRING_AGG(NAME + '-' + CONTACT_NO, ', ') AS RESPONSIBLE_PERSON
                     FROM App_Vendor_Representative
                     GROUP BY CREATEDBY
                 ),

                 WorkOrders AS (
                     SELECT DISTINCT
                         V_CODE AS vendorcode,
                         WO_NO AS workorder,
                         CONVERT(varchar, START_DATE, 103) AS from_date,
                         CONVERT(varchar, END_DATE, 103) AS to_date,
                         DEPT_CODE AS DepartmentCode,
                         TXZ01 AS Description
                     FROM App_Vendorwodetails
                     WHERE START_DATE <= '2025-07-31 23:59:59' AND END_DATE >= '2025-07-01'
                 ),

                 WageAgg AS (
                     SELECT DISTINCT
                         VendorCode,
                         WorkOrderNo,
                         ROUND(ISNULL(AVG(CAST(NULLIF(ISNULL(CAST(BasicWages AS Float), 0) + ISNULL(CAST(DAWages AS Float), 0), 0) AS Float)), 0), 2) AS BasicA,
                         ROUND(ISNULL(AVG(CAST(NULLIF(ISNULL(CAST(OtherAllow AS Float), 0), 0) AS Float)), 0), 2) AS Allowance,
                         ROUND(ISNULL(AVG(CAST(NULLIF(ISNULL(CAST(TotalWages AS Float), 0), 0) AS Float)), 0), 2) AS GrossWages,
                         ROUND(ISNULL(AVG(CAST(NULLIF(ISNULL(CAST(PfAmt AS Float), 0), 0) AS Float)), 0), 2) AS PF_DEDUCTION,
                         ROUND(ISNULL(AVG(CAST(NULLIF(ISNULL(CAST(EsiAmt AS Float), 0), 0) AS Float)), 0), 2) AS ESI_DEDUCTION,
                         ROUND(ISNULL(AVG(CAST(NULLIF(ISNULL(CAST(OtherDeduAmt AS Float), 0), 0) AS Float)), 0), 2) AS Other_DEDUCTION,
                         ROUND(ISNULL(AVG(CAST(NULLIF(ISNULL(CAST(NetWagesAmt AS Float), 0), 0) AS Float)), 0), 2) AS NET_WAGES_AMOUNT
                     FROM App_WagesDetailsJharkhand
                     WHERE MonthWage = '7' AND YearWage = '2025'
                     GROUP BY VendorCode, WorkOrderNo
                 )

                 SELECT 
                       newid() as ID,
    ROW_NUMBER() OVER (ORDER BY mis.vendorcode) AS SlNo,
                     '07' AS ProcessMonth,
                     '2025' AS ProcessYear,
                     mis.vendorcode,
                     VM.V_NAME,
                     mis.workorder,
                     mis.from_date,
                     mis.to_date,
                     ISNULL(DM.DepartmentCode, 'Work Order Not Registered') AS DepartmentCode,
                     ISNULL(DM.DepartmentName, 'Work Order Not Registered') AS DepartmentName,
                     ISNULL(LM.Location, 'Work Order Not Registered') AS Location,
                     ISNULL(CC.RESPONSIBLE_PERSON, 'Vendor Registration Not Done') AS RESPONSIBLE_PERSON_OF_THE_CONTRACTOR,
                     mis.Description as NatureOfWork,

                     SUM(CASE WHEN AA.Sex = 'M' THEN AA.TotalWorkers ELSE 0 END) AS MALE_NO_OF_MALE_WORKERS,
                     SUM(CASE WHEN AA.Sex = 'M' AND AA.Social_Category IN ('ST','SC') THEN AA.TotalWorkers ELSE 0 END) AS MALE_NOS_OF_SC_ST_WORKERS,
                     SUM(CASE WHEN AA.Sex = 'M' AND AA.Social_Category = 'OBC' THEN AA.TotalWorkers ELSE 0 END) AS MALE_NOS_OF_OBC_WORKERS,
                     SUM(CASE WHEN AA.Sex = 'M' THEN AA.TotalMandays ELSE 0 END) AS MALE_MANDAYS,
                     SUM(CASE WHEN AA.Sex = 'M' AND AA.Social_Category IN ('ST','SC') THEN AA.TotalMandays ELSE 0 END) AS MALE_MANDAYS_SC_ST,
                     SUM(CASE WHEN AA.Sex = 'M' AND AA.Social_Category = 'OBC' THEN AA.TotalMandays ELSE 0 END) AS MALE_MANDAYS_OBC,

                     SUM(CASE WHEN AA.Sex = 'F' THEN AA.TotalWorkers ELSE 0 END) AS FEMALE_NO_OF_FEMALE_WORKERS,
                     SUM(CASE WHEN AA.Sex = 'F' AND AA.Social_Category IN ('ST','SC') THEN AA.TotalWorkers ELSE 0 END) AS FEMALE_NOS_OF_SC_ST_WORKERS,
                     SUM(CASE WHEN AA.Sex = 'F' AND AA.Social_Category = 'OBC' THEN AA.TotalWorkers ELSE 0 END) AS FEMALE_NOS_OF_OBC_WORKERS,
                     SUM(CASE WHEN AA.Sex = 'F' THEN AA.TotalMandays ELSE 0 END) AS FEMALE_MANDAYS,
                     SUM(CASE WHEN AA.Sex = 'F' AND AA.Social_Category IN ('ST','SC') THEN AA.TotalMandays ELSE 0 END) AS FEMALE_MANDAYS_SC_ST,
                     SUM(CASE WHEN AA.Sex = 'F' AND AA.Social_Category = 'OBC' THEN AA.TotalMandays ELSE 0 END) AS FEMALE_MANDAYS_OBC,

                     ISNULL(SUM(AA.TotalWorkers), 0) AS Total_NOS_OF_WORKERS,
                     ISNULL(SUM(AA.TotalMandays), 0) AS Total_Mandays,

                     ISNULL((SELECT DISTINCT CONVERT(varchar(50), OW.PAYMENT_DATE, 103)
                             FROM App_Online_Wages OW
                             INNER JOIN App_Online_Wages_Details OWD
                                 ON OWD.MonthWage = OW.MonthWage AND OWD.YearWage = OW.YearWage
                                 AND OWD.VendorCode = OW.V_CODE AND OWD.WorkOrderNo = mis.workorder
                             WHERE OW.MonthWage = '7' AND OW.YearWage = '2025' AND OW.STATUS = 'Request Closed' AND OW.V_CODE = mis.vendorcode), '') AS Payment_date_WAGES,

                     ISNULL((SELECT DISTINCT OW.STATUS
                             FROM App_Online_Wages OW
                             INNER JOIN App_Online_Wages_Details OWD
                                 ON OWD.MonthWage = OW.MonthWage AND OWD.YearWage = OW.YearWage
                                 AND OWD.VendorCode = OW.V_CODE AND OWD.WorkOrderNo = mis.workorder
                             WHERE OW.MonthWage = '7' AND OW.YearWage = '2025' AND OW.STATUS = 'Request Closed' AND OW.V_CODE = mis.vendorcode), '') AS WagesStatus,

                     ISNULL((SELECT DISTINCT CONVERT(varchar(50), OW.PFChallanDate, 103)
                             FROM App_PF_ESI_Summary OW
                             INNER JOIN App_PF_ESI_Details OWD
                                 ON OWD.MonthWage = OW.MonthWage AND OWD.YearWage = OW.YearWage
                                 AND OWD.VendorCode = OW.VendorCode AND OWD.WorkOrderNo = mis.workorder
                             WHERE OW.MonthWage = '7' AND OW.YearWage = '2025' AND OW.STATUS = 'Request Closed' AND OW.VendorCode = mis.vendorcode), '') AS PFPaymentDate,

                     ISNULL((SELECT DISTINCT CONVERT(varchar(50), OW.ESIChallanDate, 103)
                             FROM App_PF_ESI_Summary OW
                             INNER JOIN App_PF_ESI_Details OWD
                                 ON OWD.MonthWage = OW.MonthWage AND OWD.YearWage = OW.YearWage
                                 AND OWD.VendorCode = OW.VendorCode AND OWD.WorkOrderNo = mis.workorder
                             WHERE OW.MonthWage = '7' AND OW.YearWage = '2025' AND OW.STATUS = 'Request Closed' AND OW.VendorCode = mis.vendorcode), '') AS ESIPaymentDate,

                     ISNULL((SELECT DISTINCT OW.Status
                             FROM App_PF_ESI_Summary OW
                             INNER JOIN App_PF_ESI_Details OWD
                                 ON OWD.MonthWage = OW.MonthWage AND OWD.YearWage = OW.YearWage
                                 AND OWD.VendorCode = OW.VendorCode AND OWD.WorkOrderNo = mis.workorder
                             WHERE OW.MonthWage = '7' AND OW.YearWage = '2025' AND OW.STATUS = 'Request Closed' AND OW.VendorCode = mis.vendorcode), '') AS PFESI_Status,

                     SUM(CASE WHEN AA.WorkManCategory = 'Unskilled' THEN AA.TotalWorkers ELSE 0 END) AS UNSKILLED_NOS_OF_WORKERS,
                     SUM(CASE WHEN AA.WorkManCategory = 'Unskilled' THEN AA.TotalMandays ELSE 0 END) AS UNSKILLED_TOTAL_MANDAYS,
                     SUM(CASE WHEN AA.WorkManCategory = 'Semi Skilled' THEN AA.TotalWorkers ELSE 0 END) AS SEMISKILLED_NOS_OF_WORKERS,
                     SUM(CASE WHEN AA.WorkManCategory = 'Semi Skilled' THEN AA.TotalMandays ELSE 0 END) AS SEMISKILLED_TOTAL_MANDAYS,

                     SUM(CASE WHEN AA.WorkManCategory = 'Skilled' THEN AA.TotalWorkers ELSE 0 END) AS SKILLED_NOS_OF_WORKERS,
                     SUM(CASE WHEN AA.WorkManCategory = 'Skilled' THEN AA.TotalMandays ELSE 0 END) AS SKILLED_TOTAL_MANDAYS,
                     SUM(CASE WHEN AA.WorkManCategory = 'Highly Skilled' THEN AA.TotalWorkers ELSE 0 END) AS HIGHLYSKILLED_NOS_OF_WORKERS,
                     SUM(CASE WHEN AA.WorkManCategory = 'Highly Skilled' THEN AA.TotalMandays ELSE 0 END) AS HIGHLYSKILLED_TOTAL_MANDAYS,
                     SUM(CASE WHEN AA.WorkManCategory = 'Other' THEN AA.TotalWorkers ELSE 0 END) AS Other_NOS_OF_WORKERS,
                     SUM(CASE WHEN AA.WorkManCategory = 'Other' THEN AA.TotalMandays ELSE 0 END) AS Other_TOTAL_MANDAYS,

                     ISNULL(WA.BasicA, 0) AS BasicDA,
                     ISNULL(WA.Allowance, 0) AS Allowance,
                     ISNULL(WA.GrossWages, 0) AS GrossWages,
                     ISNULL(WA.PF_DEDUCTION, 0) AS PF_DEDUCTION,
                     ISNULL(WA.ESI_DEDUCTION, 0) AS ESI_DEDUCTION,
                     ISNULL(WA.Other_DEDUCTION, 0) AS Other_DEDUCTION,
                     ISNULL(WA.NET_WAGES_AMOUNT, 0) AS NET_WAGES_AMOUNT,

                     CASE WHEN EXISTS (
                         SELECT 1 FROM App_Wo_Nil T
                         WHERE T.WO_NO = mis.workorder
                           AND T.NO_WORK = 'Temporary'
                           AND T.TEMPORARY_YEAR = '2025'
                           AND T.TEMPORARY_MONTH = '7'
                     ) THEN 'Yes' ELSE 'No' END AS Temporary,

                     CASE WHEN EXISTS (
                         SELECT 1 FROM App_Wo_Nil P
                         WHERE P.WO_NO = mis.workorder
                           AND P.NO_WORK = 'Permanent'
                           AND CONVERT(INT, P.TEMPORARY_YEAR + FORMAT(CONVERT(INT, P.CLOSER_DATE), '00')) <= 202507
                     ) THEN 'Yes' ELSE 'No' END AS Permanent,

                     CASE WHEN EXISTS (
                         SELECT 1 FROM APP_RECOGNIZED_WO R
                         WHERE R.WO_NO = mis.workorder
                     ) THEN 'Yes' ELSE 'No' END AS Recognized

                 FROM WorkOrders mis
                 LEFT JOIN App_VendorMaster VM ON VM.V_CODE = mis.vendorcode
                 LEFT JOIN App_DepartmentMaster DM ON DM.DepartmentCode = mis.DepartmentCode
                 LEFT JOIN App_WorkOrder_Reg WOR ON WOR.WO_NO = mis.workorder
                 LEFT JOIN App_LocationMaster LM ON LM.LocationCode = WOR.LOC_OF_WORK
                 LEFT JOIN ContractorContact CC ON CC.VendorCode = mis.vendorcode
                 LEFT JOIN AttendanceAgg AA ON AA.VendorCode = mis.vendorcode AND AA.WorkOrderNo = mis.workorder
                 LEFT JOIN WageAgg WA ON WA.VendorCode = mis.vendorcode AND WA.WorkOrderNo = mis.workorder

                 WHERE 1=1

                 GROUP BY
                     mis.vendorcode, VM.V_NAME, mis.workorder, mis.from_date, mis.to_date,
                     DM.DepartmentCode, DM.DepartmentName, LM.Location,
                     CC.RESPONSIBLE_PERSON, WA.BasicA, WA.Allowance, WA.GrossWages,
                     WA.PF_DEDUCTION, WA.ESI_DEDUCTION, WA.Other_DEDUCTION, WA.NET_WAGES_AMOUNT,
                     mis.Description

                 ORDER BY mis.vendorcode;
