select distinct fv.Pno,emp.ema_ename,Emp.ema_dept_desc,fv.DateAndTime from App_FaceVerification_Details fv
inner join SAPHRDB.dbo.T_EMPL_ALL emp 
on emp.ema_perno = fv.Pno 
where cast(DateAndTime as date) Between '2025-08-01' and '2025-11-01' and (emp.ema_pyrl_area='JS' OR emp.ema_pyrl_area='ZZ') order by fv.DateAndTime
