SELECT 
    emp.ema_perno AS Pno,
    emp.ema_ename AS EmployeeName,
    emp.ema_dept_desc AS Department,
    COUNT(DISTINCT CAST(fv.DateAndTime AS date)) AS DaysPunched
FROM SAPHRDB.dbo.T_EMPL_ALL emp
INNER JOIN App_Person ap 
    ON emp.ema_perno = ap.Pno  -- ✅ Registered employees
LEFT JOIN App_FaceVerification_Details fv 
    ON emp.ema_perno = fv.Pno
    AND CAST(fv.DateAndTime AS date) BETWEEN '2025-08-01' AND '2025-11-01'
WHERE 
    (emp.ema_pyrl_area IN ('JS', 'ZZ'))
GROUP BY 
    emp.ema_perno, emp.ema_ename, emp.ema_dept_desc
HAVING 
    COUNT(DISTINCT CAST(fv.DateAndTime AS date)) < 10  -- 👈 Adjust threshold as needed
ORDER BY 
    DaysPunched ASC;





CREATE TABLE [dbo].[App_FaceVerification_Details] (
    [ID]                   UNIQUEIDENTIFIER DEFAULT (newid()) NOT NULL,
    [Pno]                  VARCHAR (6)      NULL,
    [DateAndTime]          DATETIME         DEFAULT (getdate()) NULL,
    [PunchIn_FailedCount]  INT              NULL,
    [PunchIn_Success]      BIT              NULL,
    [PunchOut_FailedCount] INT              NULL,
    [PunchOut_Success]     BIT              NULL,
    PRIMARY KEY CLUSTERED ([ID] ASC)
);


---Not registered Yet

select ema_perno,ema_ename,ema_dept_desc from SAPHRDB.dbo.T_EMPL_ALL 
where (ema_pyrl_area='JS' or ema_pyrl_area='ZZ') and ema_perno not in (select distinct Pno from App_Person) order by ema_dept_desc
