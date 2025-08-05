string pnoSql = @"
    SELECT 
        Ema_perno, 
        Ema_perno + ' - ' + ema_ename AS ema_ename
    FROM 
        SAPHRDB.dbo.T_Empl_All 
    WHERE 
        (ema_pyrl_area = 'ZZ' OR ema_pyrl_area = 'JS') 
        AND ema_dept_desc IN (SELECT value FROM STRING_SPLIT(@DeptName, ','))
";

var rawPnos = await conn.QueryAsync<EmployeeDropdownItem>(pnoSql, new { DeptName = department });

pnoDropdown = rawPnos.Select(p => new SelectListItem
{
    Value = p.Ema_perno,
    Text = p.Ema_ename
}).ToList();
// Define a DTO
public class EmployeeDropdownItem
{
    public string Ema_perno { get; set; }
    public string Ema_ename { get; set; }
}




this is my query which returns 

 select Ema_perno, ''+Ema_perno+' - '+ema_ename as ema_ename
 FROM SAPHRDB.dbo.T_Empl_All WHERE (ema_pyrl_area = 'ZZ' or ema_pyrl_area = 'JS') and  ema_dept_desc IN (
    SELECT value FROM STRING_SPLIT('Administration & Event Management,Row,Admin & Compliances', ',')
);

this output
Ema_perno	ema_ename
155557	155557 - Pune Kumar
147605	147605 - Santosh Kumar

and this is my controller side for dropdown 

                string pnoSql = @"select ''+Ema_perno+' - '+ema_ename as ema_ename
 FROM SAPHRDB.dbo.T_Empl_All WHERE (ema_pyrl_area = 'ZZ' or ema_pyrl_area = 'JS') and  ema_dept_desc IN (
    SELECT value FROM STRING_SPLIT(@DeptName, ',')
);";
                var rawPnos = await conn.QueryAsync<string>(pnoSql, new { DeptName = department });

                pnoDropdown = rawPnos.Select(p => new SelectListItem
                {
                    Value = p,
                    Text = p
                }).ToList();

i want to show ema_ename as text and ema_perno as value 
