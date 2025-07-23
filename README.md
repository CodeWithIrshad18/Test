WITH LatestEmployeeMaster AS (
    SELECT *
    FROM (
        SELECT *, 
               ROW_NUMBER() OVER (
                   PARTITION BY AadharCard, VendorCode, WorkManSlNo 
                   ORDER BY CreatedOn DESC
               ) AS rn
        FROM App_EmployeeMaster
    ) AS em
    WHERE rn = 1
)




SELECT
    AD.VendorCode,
    AD.WorkOrderNo,
    EM.Sex,
    EM.Social_Category,
    AD.WorkManCategory,
    COUNT(DISTINCT EM.AadharCard) AS TotalWorkers,
    SUM(CAST(AD.Present AS INT)) AS TotalMandays
FROM App_AttendanceDetails AD
CROSS APPLY (
    SELECT TOP 1 EM1.Sex, EM1.Social_Category, EM1.AadharCard
    FROM App_EmployeeMaster EM1
    WHERE EM1.AadharCard = AD.AadharNo
      AND EM1.VendorCode = AD.VendorCode
      AND EM1.WorkManSlNo = AD.WorkManSl
    ORDER BY EM1.CreatedOn DESC
) AS EM
WHERE AD.Dates >= '2025-01-01'
  AND AD.Dates < '2025-02-01'
GROUP BY
    AD.VendorCode,
    AD.WorkOrderNo,
    EM.Sex,
    EM.Social_Category,
    AD.WorkManCategory;




in place of join i want subquery and take record top 1 and order by createdOn desc
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
