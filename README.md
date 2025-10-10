@{
 var DivisionDropdown = ViewBag.division as List<Division>;
}
<div class="col-md-3">
     <select asp-for="Division" class="form-control form-control-sm custom-select" name="Division">
				<option value="">Select Division</option>
				@foreach (var item in DivisionDropdown)
				{
					<option value="@item.ema_exec_head_desc">@item.ema_exec_head_desc</option>
				}
				
				</select>

</div>


public List<Division> GetDivisionDD()
{
    string connectionString = GetConnection();

    string query = @"select distinct ema_exec_head_desc from T_Empl_All where ema_exec_head_desc is not null order by ema_exec_head_desc";


    using (var connection = new SqlConnection(connectionString))
    {
        var divisions = connection.Query<Division>(query).ToList();

        return divisions;

    }

}


this is my 3 query for dropdown 

select distinct ema_exec_head_desc,ema_dept_desc from SAPHRDB.dbo.T_Empl_All 

select distinct ema_exec_head_desc,ema_dept_desc,ema_section_desc from SAPHRDB.dbo.T_Empl_All where ema_section_desc is not null

and these are my three models 
public class Division
{
    public string? ema_exec_head_desc { get; set; }

}


public class Department
{
    public string? ema_exec_head_desc { get; set; }
    public string? ema_dept_desc { get; set; }

}

public class Section
{

    public string? ema_exec_head_desc { get; set; }
    public string? ema_dept_desc { get; set; }
    public string? ema_section_desc { get; set; }

}


but i want multiple checkbox selection when one division select show department of that division and if department select then show section under that 
