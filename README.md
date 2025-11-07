SELECT 
    CAST(fv.DateAndTime AS date) AS [Date],
    COUNT(DISTINCT fv.Pno) AS TotalEmployees
FROM App_FaceVerification_Details fv
INNER JOIN SAPHRDB.dbo.T_EMPL_ALL emp 
    ON emp.ema_perno = fv.Pno
WHERE 
    CAST(fv.DateAndTime AS date) = CAST(GETDATE() AS date)
    AND (emp.ema_pyrl_area IN ('JS', 'ZZ'))
GROUP BY 
    CAST(fv.DateAndTime AS date);





select distinct fv.Pno,emp.ema_ename,Emp.ema_dept_desc,fv.DateAndTime from App_FaceVerification_Details fv
inner join SAPHRDB.dbo.T_EMPL_ALL emp 
on emp.ema_perno = fv.Pno 
where cast(DateAndTime as date) Between '2025-08-01' and '2025-11-01' and (emp.ema_pyrl_area='JS' OR emp.ema_pyrl_area='ZZ') order by fv.DateAndTime
