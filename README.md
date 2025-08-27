now add these 2 query also


  select top 1 case when Medical_status='FIT' then convert(varchar,ApprovedOn_IISM,103) else 'UNFIT/Rejected' end as HealthCheckDate from App_Online_Gatepass_Details where Addhar ='929894660928' and v_code ='10038'  
 order by CreatedOn desc

 select top 1 CreatedOn_GP,DATEDIFF(day,GatePass_Validity,GETDATE()) GatePassRenewalDate from App_Online_Gatepass_Details where Addhar ='929894660928' and v_code ='10038'
 and wo_no_gp ='4700024126' order by CreatedOn_GP desc
