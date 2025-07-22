-- Step 1: Pre-aggregate attendance by vendor, workorder, sex, category, etc.
;WITH AttendanceSummary AS (
    SELECT 
        AD.VendorCode,
        AD.WorkOrderNo,
        EM.Sex,
        EM.Social_Category,
        AD.WorkManCategory,
        COUNT(DISTINCT EM.AadharCard) AS WorkerCount,
        SUM(CAST(AD.Present AS INT)) AS TotalMandays
    FROM App_AttendanceDetails AD
    INNER JOIN App_EmployeeMaster EM 
        ON EM.AadharCard = AD.AadharNo 
        AND EM.VendorCode = AD.VendorCode 
        AND EM.WorkManSlNo = AD.WorkManSl
    WHERE 
        MONTH(AD.Dates) = 1 AND 
        YEAR(AD.Dates) = 2025 AND 
        AD.Present = 1
    GROUP BY 
        AD.VendorCode, AD.WorkOrderNo, EM.Sex, EM.Social_Category, AD.WorkManCategory
),

-- Step 2: Pre-aggregate vendor contact person info (XML STUFF logic)
VendorContacts AS (
    SELECT 
        CREATEDBY AS VendorCode,
        EMAIL = STUFF((
            SELECT DISTINCT ', ' + (NAME + '-' + CONTACT_NO)
            FROM App_Vendor_Representative b
            WHERE b.CREATEDBY = a.CREATEDBY
            FOR XML PATH(''), TYPE).value('.', 'NVARCHAR(MAX)'), 1, 2, '')
    FROM App_Vendor_Representative a
    GROUP BY CREATEDBY
)

-- Step 3: Main query with JOINs instead of subqueries
SELECT DISTINCT
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
    ISNULL(VC.EMAIL, 'Vendor Registration Not Done') AS RESPONSIBLE_PERSON_OF_THE_CONTRACTOR,

    -- MALE Workers
    ISNULL(MALE.WorkerCount, 0) AS MALE_NO_OF_MALE_WORKERS,

    -- SC/ST Male Workers
    ISNULL(SCST.WorkerCount, 0) AS MALE_NOS_OF_SC_ST_WORKERS,

    -- OBC Male Workers
    ISNULL(OBC.WorkerCount, 0) AS MALE_NOS_OF_OBC_WORKERS,

    -- Mandays
    ISNULL(MALE.TotalMandays, 0) AS MALE_MANDAYS,
    ISNULL(SCST.TotalMandays, 0) AS MALE_MANDAYS_SC_ST,

    -- Total Mandays by Category
    ISNULL(Unskilled.TotalMandays, 0) +
    ISNULL(Skilled.TotalMandays, 0) +
    ISNULL(Semi.TotalMandays, 0) +
    ISNULL(Highly.TotalMandays, 0) +
    ISNULL(Other.TotalMandays, 0) AS Total_Mandays,

    mis.Description

FROM (
    SELECT 
        V_CODE AS vendorcode,
        WO_NO AS workorder,
        CONVERT(VARCHAR, START_DATE, 103) AS from_date,
        CONVERT(VARCHAR, END_DATE, 103) AS to_date,
        DEPT_CODE AS DepartmentCode,
        TXZ01 AS Description
    FROM App_Vendorwodetails 
    WHERE START_DATE < '2025-01-31' AND END_DATE > '2025-01-01'
) mis

LEFT JOIN App_VendorMaster VM ON VM.V_CODE = mis.vendorcode
LEFT JOIN App_DepartmentMaster DM ON DM.DepartmentCode = mis.DepartmentCode
LEFT JOIN App_WorkOrder_Reg WOR ON WOR.WO_NO = mis.workorder
LEFT JOIN App_LocationMaster LM ON LM.LocationCode = WOR.LOC_OF_WORK
LEFT JOIN VendorContacts VC ON VC.VendorCode = mis.vendorcode

-- Attendance joins by group
LEFT JOIN AttendanceSummary MALE ON MALE.VendorCode = mis.vendorcode AND MALE.WorkOrderNo = mis.workorder AND MALE.Sex = 'M'
LEFT JOIN AttendanceSummary SCST ON SCST.VendorCode = mis.vendorcode AND SCST.WorkOrderNo = mis.workorder AND SCST.Sex = 'M' AND SCST.Social_Category IN ('SC', 'ST')
LEFT JOIN AttendanceSummary OBC ON OBC.VendorCode = mis.vendorcode AND OBC.WorkOrderNo = mis.workorder AND OBC.Sex = 'M' AND OBC.Social_Category = 'OBC'
LEFT JOIN AttendanceSummary Unskilled ON Unskilled.VendorCode = mis.vendorcode AND Unskilled.WorkOrderNo = mis.workorder AND Unskilled.WorkManCategory = 'Unskilled'
LEFT JOIN AttendanceSummary Semi ON Semi.VendorCode = mis.vendorcode AND Semi.WorkOrderNo = mis.workorder AND Semi.WorkManCategory = 'Semi Skilled'
LEFT JOIN AttendanceSummary Skilled ON Skilled.VendorCode = mis.vendorcode AND Skilled.WorkOrderNo = mis.workorder AND Skilled.WorkManCategory = 'Skilled'
LEFT JOIN AttendanceSummary Highly ON Highly.VendorCode = mis.vendorcode AND Highly.WorkOrderNo = mis.workorder AND Highly.WorkManCategory = 'Highly Skilled'
LEFT JOIN AttendanceSummary Other ON Other.VendorCode = mis.vendorcode AND Other.WorkOrderNo = mis.workorder AND Other.WorkManCategory = 'Other'

ORDER BY mis.vendorcode;




this is my query 
select distinct '01' as ProcessMonth,'2025' as ProcessYear, mis.vendorcode,VM.V_NAME,mis.workorder,mis.from_date,mis.to_date
,isnull(DM.DepartmentCode, 'Work Order Not Registered') as DepartmentCode,
isnull(DM.DepartmentName, 'Work Order Not Registered') as DepartmentName,
isnull(LM.Location, 'Work Order Not Registered') Location,
isnull((select top 1 EMAIL = STUFF((SELECT DISTINCT ', ' + (NAME + '-' + CONTACT_NO) from App_Vendor_Representative as b 
where b.CREATEDBY = mis.vendorcode FOR XML PATH('')), 1, 2, '') 
FROM App_Vendor_Representative as a  ),'Vendor Registration Not Done') as RESPONSIBLE_PERSON_OF_THE_CONTRACTOR ,

(select distinct count(EM.AadharCard) from App_AttendanceDetails AD inner join App_EmployeeMaster EM on
EM.AadharCard = AD.AadharNo and EM.VendorCode = AD.VendorCode and EM.WorkManSlNo = AD.WorkManSl 
and EM.Sex = 'M' where datepart(month,AD.dates)='1'
and datepart(year,AD.dates)='2025'  and AD.VendorCode = mis.vendorcode
and AD.WorkOrderNo = mis.workorder and AD.Present =1    ) as MALE_NO_OF_MALE_WORKERS ,

(select distinct count(EM.AadharCard) from App_AttendanceDetails AD inner join App_EmployeeMaster EM on
EM.AadharCard = AD.AadharNo and EM.VendorCode = AD.VendorCode and EM.WorkManSlNo = AD.WorkManSl 
and EM.Sex = 'M'  where datepart(MONTH,AD.dates)='1'
and datepart(year,AD.dates)='2025'   and AD.VendorCode = mis.vendorcode
and AD.WorkOrderNo = mis.workorder and AD.Present =1    ) as MALE_NO_OF_MALE_WORKERS ,

(select distinct count(EM.AadharCard) from App_AttendanceDetails AD inner join
App_EmployeeMaster EM on EM.AadharCard = AD.AadharNo and EM.VendorCode = AD.VendorCode and EM.WorkManSlNo = AD.WorkManSl 
and EM.Sex = 'M' and EM.Social_Category in ('ST', 'SC') where datepart(month,AD.dates)='1'
and datepart(year,AD.dates)='2025'  and AD.VendorCode = mis.vendorcode and AD.WorkOrderNo = mis.workorder 
and AD.Present =1   ) as MALE_NOS_OF_SC_ST_WORKERS,(select distinct count(EM.AadharCard) from App_AttendanceDetails AD
inner join App_EmployeeMaster EM on EM.AadharCard = AD.AadharNo and EM.VendorCode = AD.VendorCode 
and EM.WorkManSlNo = AD.WorkManSl and EM.Sex = 'M' and EM.Social_Category in ('OBC') 
where datepart(month,AD.dates)='1'and datepart(year,AD.dates)='2025' 
and AD.VendorCode = mis.vendorcode and AD.WorkOrderNo = mis.workorder and AD.Present =1   ) as MALE_NOS_OF_OBC_WORKERS,

isnull((select distinct sum(convert(int,AD.Present)) from App_AttendanceDetails AD 
inner join App_EmployeeMaster EM on EM.AadharCard = AD.AadharNo and EM.VendorCode = AD.VendorCode
and EM.WorkManSlNo = AD.WorkManSl and  EM.Sex = 'M' and AD.WorkOrderNo = mis.workorder
where datepart(month,AD.dates)='1'and datepart(year,AD.dates)='2025' 
and AD.VendorCode = mis.vendorcode),'0') as MALE_MANDAYS,

isnull((select distinct sum(convert(int,AD.Present)) 
from App_AttendanceDetails AD inner join App_EmployeeMaster EM on EM.AadharCard = AD.AadharNo 
and EM.VendorCode = AD.VendorCode and EM.WorkManSlNo = AD.WorkManSl and  EM.Sex = 'M' and EM.Social_Category in ('ST', 'SC') 
where datepart(month,AD.dates)='1'and datepart(year,AD.dates)='2025' 
and AD.VendorCode = mis.vendorcode and AD.WorkOrderNo = mis.workorder),'0') as MALE_MANDAYS_SC_ST,


(isnull((select distinct sum(convert(int,AD.Present)) from App_AttendanceDetails AD where 
datepart(month,AD.dates)='1'and datepart(year,AD.dates)='2025' 
and AD.VendorCode = mis.vendorcode and AD.WorkManCategory = 'Unskilled' 
and AD.WorkOrderNo = mis.workorder), 0) + isnull((select distinct sum(convert(int,AD.Present))
from App_AttendanceDetails AD where datepart(month,AD.dates)='1'
and datepart(year,AD.dates)='2025'  and AD.VendorCode = mis.vendorcode
and AD.WorkManCategory = 'Semi Skilled' and AD.WorkOrderNo = mis.workorder),0)+ isnull((select distinct sum(convert(int,AD.Present))
from App_AttendanceDetails AD where datepart(month,AD.dates)='1'
and datepart(year,AD.dates)='2025'  and AD.VendorCode = mis.vendorcode 
and AD.WorkManCategory = 'Skilled' and AD.WorkOrderNo = mis.workorder),0)+  isnull((select distinct sum(convert(int,AD.Present))
from App_AttendanceDetails AD where datepart(month,AD.dates)='1'and 
datepart(year,AD.dates)='2025'  and AD.VendorCode = mis.vendorcode 
and AD.WorkManCategory = 'Highly Skilled' and AD.WorkOrderNo = mis.workorder),0)+ isnull((select distinct sum(convert(int,AD.Present))
from App_AttendanceDetails AD where datepart(month,AD.dates)='1'and 
datepart(year,AD.dates)='2025'  and AD.VendorCode = mis.vendorcode and AD.WorkManCategory = 'Other' 
and AD.WorkOrderNo = mis.workorder),0) ) as Total_Mandays,mis.Description from (select V_CODE as vendorcode, WO_NO as workorder,
Convert(varchar, START_DATE, 103) as from_date, Convert(varchar, END_DATE, 103) as to_date, DEPT_CODE as DepartmentCode,
TXZ01 as Description from App_Vendorwodetails where START_DATE < '2025-01-31' and END_DATE > '2025-1-01 00:00:00.000')
mis  left join App_VendorMaster VM on VM.V_CODE = mis.vendorcode  left join
App_DepartmentMaster DM on DM.DepartmentCode = mis.DepartmentCode  left join App_WorkOrder_Reg WOR on WOR.WO_NO = mis.workorder 
left join App_LocationMaster LM on LM.LocationCode = WOR.LOC_OF_WORK  where  1=1  order by mis.vendorcode 


when i run this query it taking so much time like 6 to 10 mins why?
