SELECT
    DATENAME(MONTH, fv.DateAndTime) AS [MonthName],
    COUNT(DISTINCT fv.Pno) AS TotalEmployees
FROM App_FaceVerification_Details fv
INNER JOIN SAPHRDB.dbo.T_EMPL_ALL emp
    ON emp.ema_perno = fv.Pno
WHERE
    CAST(fv.DateAndTime AS date) BETWEEN '2025-11-01' AND '2025-11-25'
    AND (emp.ema_pyrl_area IN ('JS', 'ZZ'))
GROUP BY
    DATENAME(MONTH, fv.DateAndTime),
    MONTH(fv.DateAndTime)
ORDER BY
    MONTH(fv.DateAndTime);
