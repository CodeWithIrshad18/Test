this is my query , in this query i want top 1 and order by createdOn desc only
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
