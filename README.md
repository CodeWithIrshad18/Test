select PM.CompetencyType,PM.ID,ModuleID,ModuleName,(cast(FromDate as varchar(11))+' ' +FromTime) as Fromdate,
cast((cast(Todate as varchar(11))+' ' +ToTime) as datetime) as Todate,
Venue,NoofEmpl=(select count(*) from App_PlanDetails where PM.ID=PlanID),PM.CreatedBY from App_PlanMaster PM 
inner join App_Modules md on PM.ModuleId=MD.ID 
inner join App_CompetencyMaster AC on md.CompetencyID=AC.ID  and PM.CompetencyType='1'
and DATEDIFF(month,cast((cast(ToDate as varchar(11))+ ' ' + ToTime) as datetime),GETDATE())>=2 and PM.ID='79d12552-7c50-43e1-81d5-cb76703e6c72'


select distinct PM.CompetencyType,PD.ID,PD.PlanID,PD.EmployeeID,PD.Marks,PD.CreatedBy,PM.ModuleID,EM.Pno,DM.Designation,EM.[Name],PD.Planned,PD.Attendence,
PD.TrainingRating,PD.TNI_Type,PD.Priority,1 as ind,RL,PL,CL,DepartmentName,PD.HOD_Rating,PD.Remarks_HodRating From App_Plandetails PD 
inner join App_PlanMaster PM on PD.PlanID =PM.Id inner join App_EmployeeMaster EM on PD.EmployeeID=EM.ID left join App_DepartmentMaster 
DP on  EM.DepartmentID=DP.ID inner join App_DesignationMaster DM on EM.DesignationID = DM.ID  inner join  App_Modules MM on PM.ModuleID 
= MM.ID inner join App_competencyDetails CD on MM.CompetencyID=CD.CompetencyID  and PD.EmployeeID=cd.EmployeeID and PM.FinyearID=CD.FinyearID
where PD.PlanID='79D12552-7C50-43E1-81D5-CB76703E6C72'
