this is the query that gives me 105 count in MaleMandays but when i run below query it gaves me 109 and 109 is current count
  WITH RankedAttendance AS (
    SELECT
        AD.VendorCode,
        AD.WorkOrderNo,
        EM.Sex,
        EM.Social_Category,
        AD.WorkManCategory,
        EM.AadharCard,
        CAST(AD.Present AS INT) AS Present,
        AD.dates
    FROM App_AttendanceDetails AD
  
  OUTER APPLY (
        SELECT TOP 1 *
        FROM App_EmployeeMaster EM
        WHERE EM.AadharCard = AD.AadharNo
          AND EM.VendorCode = AD.VendorCode
           AND EM.WorkManSlNo = AD.WorkManSl
          
     
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









-------------------------------------------------
   
test with it

select distinct AadharNo, sum(convert(int,present)),
(select top 1 Sex from App_EmployeeMaster where VendorCode='10038' and AadharCard=AadharNo )
from App_AttendanceDetails att 
where att.VendorCode='10038' and datepart(MONTH,Dates) =07 and DATEPART(year,dates)=2025 and WorkOrderNo='4700024126'
group by AadharNo

