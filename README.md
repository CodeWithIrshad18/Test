private string GetCurrentFinYear()
{
    var today = DateTime.Now;
    int year = today.Month >= 4 ? today.Year : today.Year - 1;
    int nextYear = year + 1;
    return $"FY{nextYear.ToString().Substring(2)}"; // e.g. FY26
}

public IActionResult Create()
{
    var finYears = GetFinYearDD();
    var currentFY = GetCurrentFinYear();

    ViewBag.FinYearDropdown = finYears;
    ViewBag.CurrentFY = currentFY;

    return View();
}

<select class="form-control form-control-sm custom-select me-2" 
        name="FinYearID" id="FinYearID" disabled>
    @foreach (var item in ViewBag.FinYearDropdown)
    {
        var selected = item.FinYear == ViewBag.CurrentFY ? "selected" : "";
        <option value="@item.Id" @selected>@item.FinYear</option>
    }
</select>

<!-- Hidden field to still POST the ID -->
<input type="hidden" name="FinYearID" 
       value="@(ViewBag.FinYearDropdown.FirstOrDefault(f => f.FinYear == ViewBag.CurrentFY)?.Id)" />



public List<FinYearDD> GetFinYearDD()
{
    string connectionString = GetSAPConnectionString();

    string query = @"select Id,FinYear from App_Sys_FinYear";

    using (var connection = new SqlConnection(connectionString))
    {
        var finYearDDs = connection.Query<FinYearDD>(query).ToList();

        return finYearDDs;

    }

}

Dropdown 

 <select class="form-control form-control-sm custom-select  me-2" name="Dept" style="height:80%;">
		<option value=""></option>
		@foreach (var item in FinYearDropdown)
		{
						<option value="@item.FinYear">@item.FinYear</option>
		}
		
		</select>

Data:

858C5BF6-2548-4BBE-A7E1-20A159910260	FY26
4D0F57EE-443B-4B70-AF85-BAE7FA0651DD	FY27
4B65AC79-92D3-46FA-B99E-D88E33BDAC9F	FY28
4F4C45C0-C042-479C-ADD9-F55D327B2DFE	FY29
A7C4CD7B-3AA6-4C69-AE62-9D03322287FC	FY30
