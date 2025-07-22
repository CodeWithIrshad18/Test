WITH AttendanceAgg AS (
    SELECT
        AD.VendorCode,
        AD.WorkOrderNo,
        (
            SELECT EM.Sex
            FROM App_EmployeeMaster EM
            WHERE EM.AadharCard = AD.AadharNo
              AND EM.VendorCode = AD.VendorCode
              AND EM.WorkManSlNo = AD.WorkManSl
        ) AS Sex,
        (
            SELECT EM.Social_Category
            FROM App_EmployeeMaster EM
            WHERE EM.AadharCard = AD.AadharNo
              AND EM.VendorCode = AD.VendorCode
              AND EM.WorkManSlNo = AD.WorkManSl
        ) AS Social_Category,
        AD.WorkManCategory,
        COUNT(DISTINCT AD.AadharNo) AS TotalWorkers,
        SUM(CAST(AD.Present AS INT)) AS TotalMandays
    FROM App_AttendanceDetails AD
    WHERE AD.dates >= '2025-01-01' AND AD.dates < '2025-02-01'
    GROUP BY AD.VendorCode, AD.WorkOrderNo, AD.WorkManCategory
)




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
