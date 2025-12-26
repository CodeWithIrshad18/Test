select Rs.ReqNo as BarricadingRequestNo,Rs.CreatedOn as BarricadingRequestOn,Rs.CreatedBy as BarricadingRequestBy,Dv.Division as Division,Dp.Department as Department,Rs.Start_Date as StartDate,Rs.End_Date as EndDate,
Rs.GISNo as GISApprovalNo,Rs.SafetyHead_Pno as SafetyApprovedBy,Rs.SafetyHead_CreatedOn as SafetyApprovedOn,Rs.SectionHead_Pno as SectionalInchargeApprovedBy,
Rs.SectionHead_CreatedOn as SectionalInchargeApprovedOn,Rs.SafetyHead_CreatedOn as RequestClosedOn,Rs.SafetyHead_Pno as RequestClosedBy
from App_Road_side_barricade_Entry as RS 

inner join App_DivisionMaster as Dv on Dv.ID = RS.Info_DivisionID
inner join App_DepartmentMaster as Dp on Dp.ID = RS.Info_DepartmentID
where Start_Date>='2025-12-01' and End_Date<='2025-12-26' order by Start_Date
