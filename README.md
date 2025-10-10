public IActionResult GetDivisions()
{
    var connectionString = GetConnection();
    string query = @"select distinct ema_exec_head_desc 
                     from SAPHRDB.dbo.T_Empl_All 
                     where ema_exec_head_desc is not null 
                     order by ema_exec_head_desc";

    using (var connection = new SqlConnection(connectionString))
    {
        var divisions = connection.Query<Division>(query).ToList();
        return Json(divisions);
    }
}

public IActionResult GetDepartments(string division)
{
    var connectionString = GetConnection();
    string query = @"select distinct ema_exec_head_desc, ema_dept_desc 
                     from SAPHRDB.dbo.T_Empl_All 
                     where ema_exec_head_desc = @division 
                     and ema_dept_desc is not null
                     order by ema_dept_desc";

    using (var connection = new SqlConnection(connectionString))
    {
        var departments = connection.Query<Department>(query, new { division }).ToList();
        return Json(departments);
    }
}

public IActionResult GetSections(string division, string department)
{
    var connectionString = GetConnection();
    string query = @"select distinct ema_exec_head_desc, ema_dept_desc, ema_section_desc 
                     from SAPHRDB.dbo.T_Empl_All 
                     where ema_exec_head_desc = @division 
                     and ema_dept_desc = @department 
                     and ema_section_desc is not null
                     order by ema_section_desc";

    using (var connection = new SqlConnection(connectionString))
    {
        var sections = connection.Query<Section>(query, new { division, department }).ToList();
        return Json(sections);
    }
}

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" />
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-multiselect@1.1.1/dist/css/bootstrap-multiselect.css">

<script src="https://code.jquery.com/jquery-3.6.4.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/bootstrap-multiselect@1.1.1/dist/js/bootstrap-multiselect.min.js"></script>

<div class="row">
    <div class="col-md-4">
        <label><b>Division</b></label>
        <select id="divisionSelect" multiple="multiple"></select>
    </div>

    <div class="col-md-4">
        <label><b>Department</b></label>
        <select id="departmentSelect" multiple="multiple"></select>
    </div>

    <div class="col-md-4">
        <label><b>Section</b></label>
        <select id="sectionSelect" multiple="multiple"></select>
    </div>
</div>

<script>
$(document).ready(function() {
    // Initialize multiselect dropdowns
    $('#divisionSelect, #departmentSelect, #sectionSelect').multiselect({
        includeSelectAllOption: true,
        enableFiltering: true,
        enableCaseInsensitiveFiltering: true,
        buttonWidth: '100%',
        maxHeight: 300,
        nonSelectedText: 'Select options'
    });

    // Load divisions
    $.getJSON('/YourController/GetDivisions', function(data) {
        $('#divisionSelect').empty();
        $.each(data, function(i, item) {
            $('#divisionSelect').append(`<option value="${item.ema_exec_head_desc}">${item.ema_exec_head_desc}</option>`);
        });
        $('#divisionSelect').multiselect('rebuild');
    });

    // When division changes → load departments
    $('#divisionSelect').on('change', function() {
        var selectedDivisions = $(this).val() || [];
        $('#departmentSelect').empty().multiselect('rebuild');
        $('#sectionSelect').empty().multiselect('rebuild');

        $.each(selectedDivisions, function(i, division) {
            $.getJSON('/YourController/GetDepartments', { division: division }, function(data) {
                $.each(data, function(i, dept) {
                    $('#departmentSelect').append(`<option value="${dept.ema_dept_desc}" data-division="${dept.ema_exec_head_desc}">
                        ${dept.ema_exec_head_desc} - ${dept.ema_dept_desc}
                    </option>`);
                });
                $('#departmentSelect').multiselect('rebuild');
            });
        });
    });

    // When department changes → load sections
    $('#departmentSelect').on('change', function() {
        $('#sectionSelect').empty().multiselect('rebuild');

        $('#departmentSelect option:selected').each(function() {
            var division = $(this).data('division');
            var department = $(this).val();

            $.getJSON('/YourController/GetSections', { division: division, department: department }, function(data) {
                $.each(data, function(i, sec) {
                    $('#sectionSelect').append(`<option value="${sec.ema_section_desc}">
                        ${sec.ema_exec_head_desc} - ${sec.ema_dept_desc} - ${sec.ema_section_desc}
                    </option>`);
                });
                $('#sectionSelect').multiselect('rebuild');
            });
        });
    });
});
</script>

.multiselect-container>li>a>label {
  padding: 4px 10px;
  font-size: 14px;
}




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
