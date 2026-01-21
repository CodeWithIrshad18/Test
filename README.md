SELECT  distinct Pno,emp.ema_dept_desc,cast(fv.DateAndTime as Date) as Date
FROM App_FaceVerification_Details fv
INNER JOIN SAPHRDB.dbo.T_EMPL_ALL emp
    ON emp.ema_perno = fv.Pno
WHERE
    CAST(fv.DateAndTime AS date) BETWEEN '2025-11-01' AND '2026-01-25'
    AND (emp.ema_pyrl_area IN ('JS', 'ZZ'))
    order by Date
