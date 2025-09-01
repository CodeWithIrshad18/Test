IIF(DATEDIFF(DAY, g.GatePass_Validity, GETDATE()) < 0, 0, DATEDIFF(DAY, g.GatePass_Validity, GETDATE())) AS GatePassRenewalDate

 
 
 
 DATEDIFF(DAY, g.GatePass_Validity, GETDATE()) AS GatePassRenewalDate
