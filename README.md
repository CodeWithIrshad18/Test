-- Declare input parameters
DECLARE @WO_NO VARCHAR(20) = '4700024406';
DECLARE @ProcessYear VARCHAR(4) = '2025';
DECLARE @ProcessMonth VARCHAR(2) = '1';

-- Combine ProcessYear and ProcessMonth into single int like 202501
DECLARE @ProcessYM INT = CAST(@ProcessYear + RIGHT('00' + @ProcessMonth, 2) AS INT);

-- Final result query
SELECT
    @WO_NO AS WO_NO,

    -- 1. Check for Temporary work in App_Wo_Nil
    CASE
        WHEN EXISTS (
            SELECT 1
            FROM App_Wo_Nil
            WHERE WO_NO = @WO_NO
              AND NO_WORK = 'Temporary'
              AND TEMPORARY_YEAR = @ProcessYear
              AND TEMPORARY_MONTH = @ProcessMonth
        )
        THEN 'Yes'
        ELSE 'No'
    END AS Has_Temporary_Work,

    -- 2. Check for Permanent work with closure date ≤ ProcessYM
    CASE
        WHEN EXISTS (
            SELECT 1
            FROM App_Wo_Nil
            WHERE WO_NO = @WO_NO
              AND NO_WORK = 'Permanent'
              AND CONVERT(INT, TEMPORARY_YEAR + FORMAT(CONVERT(INT, CLOSER_DATE), '00')) <= @ProcessYM
        )
        THEN 'Yes'
        ELSE 'No'
    END AS Has_Permanent_Work,

    -- 3. Check if work order is active in APP_RECOGNIZED_WO
    CASE
        WHEN EXISTS (
            SELECT 1
            FROM APP_RECOGNIZED_WO
            WHERE WO_NO = @WO_NO
        )
        THEN 'Yes'
        ELSE 'No'
    END AS Is_Recognized_WorkOrder;




this is my query and i want to integrate the changes you provide to this query
WITH AttendanceAgg AS (
    SELECT
        AD.VendorCode,
        AD.WorkOrderNo,
        EM.Sex,
        EM.Social_Category,
        AD.WorkManCategory,
        COUNT(DISTINCT EM.AadharCard) AS TotalWorkers,
        SUM(CAST(AD.Present AS INT)) AS TotalMandays
    FROM App_AttendanceDetails AD
    INNER JOIN App_EmployeeMaster EM
        ON EM.AadharCard = AD.AadharNo
        AND EM.VendorCode = AD.VendorCode
        AND EM.WorkManSlNo = AD.WorkManSl
    WHERE AD.dates >= '2025-01-01' AND AD.dates < '2025-02-01'
    GROUP BY AD.VendorCode, AD.WorkOrderNo, EM.Sex, EM.Social_Category, AD.WorkManCategory
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
    WHERE START_DATE <= '2025-01-31' AND END_DATE >= '2025-01-01'
),

WageAgg AS (
    SELECT distinct
        VendorCode,
        WorkOrderNo,
       ROUND(ISNULL(AVG(CAST(NULLIF(ISNULL(CAST(BasicWages AS FLOAT), 0) + ISNULL(CAST(DAWages AS FLOAT), 0), 0) AS FLOAT)), 0), 2) AS BasicA,
      
      ROUND(ISNULL(AVG(CAST(NULLIF(ISNULL(CAST(OtherAllow AS FLOAT), 0), 0) AS FLOAT)), 0), 2) AS Allowance,
        ROUND(ISNULL(AVG(CAST(NULLIF(ISNULL(CAST(TotalWages AS FLOAT), 0), 0) AS FLOAT)), 0), 2) AS GrossWages,
        ROUND(ISNULL(AVG(CAST(NULLIF(ISNULL(CAST(PfAmt AS FLOAT), 0), 0) AS FLOAT)), 0), 2) AS PF_DEDUCTION,
        ROUND(ISNULL(AVG(CAST(NULLIF(ISNULL(CAST(EsiAmt AS FLOAT), 0), 0) AS FLOAT)), 0), 2) AS ESI_DEDUCTION,
        ROUND(ISNULL(AVG(CAST(NULLIF(ISNULL(CAST(OtherDeduAmt AS FLOAT), 0), 0) AS FLOAT)), 0), 2) AS Other_DEDUCTION,
        ROUND(ISNULL(AVG(CAST(NULLIF(ISNULL(CAST(NetWagesAmt AS FLOAT), 0), 0) AS FLOAT)), 0), 2) AS NET_WAGES_AMOUNT
    FROM App_WagesDetailsJharkhand
    WHERE MonthWage = '1' AND YearWage = '2025'
    GROUP BY VendorCode, WorkOrderNo
)

SELECT
    '01' AS ProcessMonth,
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

    -- Male counts
    SUM(CASE WHEN AA.Sex = 'M' THEN AA.TotalWorkers ELSE 0 END) AS MALE_NO_OF_MALE_WORKERS,
    SUM(CASE WHEN AA.Sex = 'M' AND AA.Social_Category IN ('ST','SC') THEN AA.TotalWorkers ELSE 0 END) AS MALE_NOS_OF_SC_ST_WORKERS,
    SUM(CASE WHEN AA.Sex = 'M' AND AA.Social_Category = 'OBC' THEN AA.TotalWorkers ELSE 0 END) AS MALE_NOS_OF_OBC_WORKERS,
    SUM(CASE WHEN AA.Sex = 'M' THEN AA.TotalMandays ELSE 0 END) AS MALE_MANDAYS,
    SUM(CASE WHEN AA.Sex = 'M' AND AA.Social_Category IN ('ST','SC') THEN AA.TotalMandays ELSE 0 END) AS MALE_MANDAYS_SC_ST,
    SUM(CASE WHEN AA.Sex = 'M' AND AA.Social_Category = 'OBC' THEN AA.TotalMandays ELSE 0 END) AS MALE_MANDAYS_OBC,

    -- Female counts
    SUM(CASE WHEN AA.Sex = 'F' THEN AA.TotalWorkers ELSE 0 END) AS FEMALE_NO_OF_FEMALE_WORKERS,
    SUM(CASE WHEN AA.Sex = 'F' AND AA.Social_Category IN ('ST','SC') THEN AA.TotalWorkers ELSE 0 END) AS FEMALE_NOS_OF_SC_ST_WORKERS,
    SUM(CASE WHEN AA.Sex = 'F' AND AA.Social_Category = 'OBC' THEN AA.TotalWorkers ELSE 0 END) AS FEMALE_NOS_OF_OBC_WORKERS,
    SUM(CASE WHEN AA.Sex = 'F' THEN AA.TotalMandays ELSE 0 END) AS FEMALE_MANDAYS,
    SUM(CASE WHEN AA.Sex = 'F' AND AA.Social_Category IN ('ST','SC') THEN AA.TotalMandays ELSE 0 END) AS FEMALE_MANDAYS_SC_ST,
    SUM(CASE WHEN AA.Sex = 'F' AND AA.Social_Category = 'OBC' THEN AA.TotalMandays ELSE 0 END) AS FEMALE_MANDAYS_OBC,



      -- Totals
    (IsNull(SUM(AA.TotalWorkers),0)) AS Total_NOS_OF_WORKERS,
    (IsNull(SUM(AA.TotalMandays),0)) AS Total_Mandays,

    -- PF / ESI / Wages Dates & Status
    ISNULL((SELECT DISTINCT CONVERT(varchar(50), OW.PAYMENT_DATE, 103) 
            FROM App_Online_Wages OW 
            INNER JOIN App_Online_Wages_Details OWD 
                ON OWD.MonthWage = OW.MonthWage AND OWD.YearWage = OW.YearWage 
                AND OWD.VendorCode = OW.V_CODE AND OWD.WorkOrderNo = mis.workorder 
            WHERE OW.MonthWage ='1' AND OW.YearWage = '2025' AND OW.STATUS = 'Request Closed' AND OW.V_CODE = mis.vendorcode), '') 
        AS Payment_date_WAGES,

    ISNULL((SELECT DISTINCT OW.STATUS 
            FROM App_Online_Wages OW 
            INNER JOIN App_Online_Wages_Details OWD 
                ON OWD.MonthWage = OW.MonthWage AND OWD.YearWage = OW.YearWage 
                AND OWD.VendorCode = OW.V_CODE AND OWD.WorkOrderNo = mis.workorder 
            WHERE OW.MonthWage = '1' AND OW.YearWage = '2025' AND OW.STATUS = 'Request Closed' AND OW.V_CODE = mis.vendorcode), '') 
        AS WagesStatus,

    ISNULL((SELECT DISTINCT CONVERT(varchar(50), OW.PFChallanDate, 103) 
            FROM App_PF_ESI_Summary OW 
            INNER JOIN App_PF_ESI_Details OWD 
                ON OWD.MonthWage = OW.MonthWage AND OWD.YearWage = OW.YearWage 
                AND OWD.VendorCode = OW.VendorCode AND OWD.WorkOrderNo = mis.workorder 
            WHERE OW.MonthWage = '1' AND OW.YearWage = '2025' AND OW.STATUS = 'Request Closed' AND OW.VendorCode = mis.vendorcode), '') 
        AS PFPaymentDate,

    ISNULL((SELECT DISTINCT CONVERT(varchar(50), OW.ESIChallanDate, 103) 
            FROM App_PF_ESI_Summary OW 
            INNER JOIN App_PF_ESI_Details OWD 
                ON OWD.MonthWage = OW.MonthWage AND OWD.YearWage = OW.YearWage 
                AND OWD.VendorCode = OW.VendorCode AND OWD.WorkOrderNo = mis.workorder 
            WHERE OW.MonthWage = '1' AND OW.YearWage = '2025' AND OW.STATUS = 'Request Closed' AND OW.VendorCode = mis.vendorcode), '') 
        AS ESIPaymentDate,

    ISNULL((SELECT DISTINCT OW.Status 
            FROM App_PF_ESI_Summary OW 
            INNER JOIN App_PF_ESI_Details OWD 
                ON OWD.MonthWage = OW.MonthWage AND OWD.YearWage = OW.YearWage 
                AND OWD.VendorCode = OW.VendorCode AND OWD.WorkOrderNo = mis.workorder 
            WHERE OW.MonthWage = '1' AND OW.YearWage = '2025' AND OW.STATUS = 'Request Closed' AND OW.VendorCode = mis.vendorcode), '') 
        AS PFESI_Status,

    -- Skill Level Counts
    SUM(CASE WHEN AA.WorkManCategory = 'Unskilled' THEN AA.TotalWorkers ELSE 0 END) AS UNSKILLED_NOS_OF_WORKERS,
    SUM(CASE WHEN AA.WorkManCategory = 'Unskilled' THEN AA.TotalMandays ELSE 0 END) AS UNSKILLED_TOTAL_MANDAYS,
    SUM(CASE WHEN AA.WorkManCategory = 'Semi Skilled' THEN AA.TotalWorkers ELSE 0 END) AS SEMISKILLED_NOS_OF_WORKERS,
    SUM(CASE WHEN AA.WorkManCategory = 'Skilled' THEN AA.TotalWorkers ELSE 0 END) AS SKILLED_NOS_OF_WORKERS,
    SUM(CASE WHEN AA.WorkManCategory = 'Skilled' THEN AA.TotalMandays ELSE 0 END) AS SKILLED_TOTAL_MANDAYS,
    SUM(CASE WHEN AA.WorkManCategory = 'Highly Skilled' THEN AA.TotalWorkers ELSE 0 END) AS HIGHLYSKILLED_NOS_OF_WORKERS,
    SUM(CASE WHEN AA.WorkManCategory = 'Highly Skilled' THEN AA.TotalMandays ELSE 0 END) AS HIGHLYSKILLED_TOTAL_MANDAYS,
    SUM(CASE WHEN AA.WorkManCategory = 'Other' THEN AA.TotalWorkers ELSE 0 END) AS Other_NOS_OF_WORKERS,
    SUM(CASE WHEN AA.WorkManCategory = 'Other' THEN AA.TotalMandays ELSE 0 END) AS Other_TOTAL_MANDAYS,

  

    -- Average Wages (added)
   (ISNULL(WA.BasicA,0)) as BasicDA,
    (ISNULL(WA.Allowance,0)) as Allowance,
   (ISNULL(WA.GrossWages,0)) as GrossWages,
    (ISNULL(WA.PF_DEDUCTION,0)) as PF_DEDUCTION,
   (ISNULL(WA.ESI_DEDUCTION,0)) as ESI_DEDUCTION,
     (ISNULL(WA.Other_DEDUCTION,0)) as Other_DEDUCTION,
   (ISNULL(WA.NET_WAGES_AMOUNT,0)) as NET_WAGES_AMOUNT




  

FROM WorkOrders mis
LEFT JOIN App_VendorMaster VM ON VM.V_CODE = mis.vendorcode
LEFT JOIN App_DepartmentMaster DM ON DM.DepartmentCode = mis.DepartmentCode
LEFT JOIN App_WorkOrder_Reg WOR ON WOR.WO_NO = mis.workorder
LEFT JOIN App_LocationMaster LM ON LM.LocationCode = WOR.LOC_OF_WORK
LEFT JOIN ContractorContact CC ON CC.VendorCode = mis.vendorcode
LEFT JOIN AttendanceAgg AA ON AA.VendorCode = mis.vendorcode AND AA.WorkOrderNo = mis.workorder
LEFT JOIN WageAgg WA ON WA.VendorCode = mis.vendorcode AND WA.WorkOrderNo = mis.workorder

GROUP BY
    mis.vendorcode, VM.V_NAME, mis.workorder, mis.from_date, mis.to_date,
    DM.DepartmentCode, DM.DepartmentName, LM.Location,
    CC.RESPONSIBLE_PERSON, WA.BasicA, WA.Allowance, WA.GrossWages,
    WA.PF_DEDUCTION, WA.ESI_DEDUCTION, WA.Other_DEDUCTION, WA.NET_WAGES_AMOUNT,
    mis.Description

ORDER BY mis.vendorcode;
