int pageSize = 5;
int skip = (page - 1) * pageSize;

string sqlQuery = @"
SELECT 
    k.ID,
    k.KPIDetails,
    k.KPILevel,
    p.PeriodicityName,
    u.UnitName,
    ps.PerspectiveName,
    t.TypeofKPIName,
    k.Division,
    k.Department,
    k.GoodPerformance,
    k.KPICode
FROM AppKpiMasters k
LEFT JOIN AppPeriodicities p ON k.PeriodicityID = p.ID
LEFT JOIN AppUnits u ON k.UnitID = u.ID
LEFT JOIN AppPerspectives ps ON k.PerspectiveID = ps.ID
LEFT JOIN AppTypeofKPIs t ON k.TypeofKPIID = t.ID
WHERE (@search IS NULL OR k.KPILevel LIKE '%' + @search + '%')
ORDER BY k.KPIDetails
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;
";

string countQuery = @"
SELECT COUNT(*) 
FROM AppKpiMasters k
WHERE (@search IS NULL OR k.KPILevel LIKE '%' + @search + '%');
";

var searchParam = new SqlParameter("@search", string.IsNullOrEmpty(searchString) ? DBNull.Value : searchString);
var skipParam = new SqlParameter("@skip", skip);
var takeParam = new SqlParameter("@take", pageSize);

// Fetch paginated data
var pagedData = await context.Database
    .SqlQueryRaw<KpiListDto>(sqlQuery, searchParam, skipParam, takeParam)
    .ToListAsync();

// Fetch total count
int totalCount = await context.Database
    .SqlQueryRaw<int>(countQuery, searchParam)
    .FirstAsync();

ViewBag.ListData2 = pagedData;
ViewBag.CurrentPage = page;
ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
ViewBag.searchString = searchString;


public class KpiListDto
{
    public Guid ID { get; set; }
    public string KPIDetails { get; set; }
    public string KPILevel { get; set; }
    public string PeriodicityName { get; set; }
    public string UnitName { get; set; }
    public string PerspectiveName { get; set; }
    public string TypeofKPIName { get; set; }
    public string Division { get; set; }
    public string Department { get; set; }
    public string GoodPerformance { get; set; }
    public string KPICode { get; set; }
}


<thead>
    <tr>
        <th>KPI</th>
        <th>UOM</th>
        <th>KPI Level</th>
        <th>Periodicity</th>
        <th>Division</th>
        <th>Department</th>
        <th>Good Performance</th>
        <th>KPI Code</th>
    </tr>
</thead>
<tbody>
    @if (ViewBag.ListData2 != null)
    {
        @foreach (var item in ViewBag.ListData2)
        {
            <tr>
                <td>
                    <a href="#" class="refNoLink"
                       data-id="@item.ID"
                       data-KPIDetails="@item.KPIDetails"
                       data-KPILevel="@item.KPILevel"
                       data-UnitName="@item.UnitName"
                       data-PeriodicityName="@item.PeriodicityName"
                       data-PerspectiveName="@item.PerspectiveName"
                       data-TypeofKPIName="@item.TypeofKPIName"
                       data-Division="@item.Division"
                       data-Department="@item.Department"
                       data-GoodPerformance="@item.GoodPerformance"
                       data-KPICode="@item.KPICode">
                        @item.KPIDetails
                    </a>
                </td>
                <td>@item.UnitName</td>
                <td>@item.KPILevel</td>
                <td>@item.PeriodicityName</td>
                <td>@item.Division</td>
                <td>@item.Department</td>
                <td>@item.GoodPerformance</td>
                <td>@item.KPICode</td>
            </tr>
        }
    }
    else
    {
        <tr>
            <td colspan="8" class="text-center text-muted py-3">No data available</td>
        </tr>
    }
</tbody>




this is my controller 

public async Task<IActionResult> CreateKPI(Guid? id, int page = 1, string searchString = "")
{
    if (HttpContext.Session.GetString("Session") != null)
    {
        var UserId = HttpContext.Session.GetString("Session");

        ViewBag.user = User;


        var userIdString = HttpContext.Session.GetString("Session");
        if (string.IsNullOrEmpty(userIdString))
        {
            return RedirectToAction("AccessDenied", "TPR");
        }



        string formName = "CreateKPI";
        var form = await context.AppFormDetails
            .Where(f => f.FormName == formName)
            .Select(f => f.Id)
            .FirstOrDefaultAsync();

        if (form == default)
        {
            return RedirectToAction("AccessDenied", "TPR");
        }

        bool canModify = await context.AppUserFormPermissions
            .Where(p => p.UserId == UserId && p.FormId == form)
            .AnyAsync(p => p.AllowModify == true);

        bool canDelete = await context.AppUserFormPermissions
            .Where(p => p.UserId == UserId && p.FormId == form)
            .AnyAsync(p => p.AllowDelete == true);
        bool canWrite = await context.AppUserFormPermissions
            .Where(p => p.UserId == UserId && p.FormId == form)
            .AnyAsync(p => p.AllowWrite == true);

        ViewBag.CanModify = canModify;
        ViewBag.CanDelete = canDelete;
        ViewBag.CanWrite = canWrite;

        int pageSize = 5;
        var query = context.AppKpiMasters.AsQueryable();

        if (!string.IsNullOrEmpty(searchString))
        {
            query = query.Where(a => a.KPILevel.Contains(searchString));
        }

        var pagedData = await query.Skip((page - 1) * pageSize).Take(pageSize).ToListAsync();
        var totalCount = query.Count();

        ViewBag.ListData2 = pagedData;
        ViewBag.CurrentPage = page;
        ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
        ViewBag.searchString = searchString;

        var Area = GetAreaDD();
        ViewBag.Area = Area;

        var type = GetKPITypeDD();
        ViewBag.Type = type;

        var unit = GetUnitDD();
        ViewBag.Unit = unit;

        var Periodicity = GetPeriodicityDD();
        ViewBag.Periodicity = Periodicity;

        var perforamnce = GetPerformanceDD();
        ViewBag.perforamnce = perforamnce;

        var division = GetDivisionDD();
        ViewBag.division = division;

        using (var connection = new SqlConnection(GetConnection()))
        {
            string query2 = @"
        SELECT DISTINCT ema_exec_head_desc 
        FROM SAPHRDB.dbo.T_Empl_All
        WHERE ema_exec_head_desc IS NOT NULL
        ORDER BY ema_exec_head_desc";

            var divisions = connection.Query<Division>(query2).ToList();
            ViewBag.DivisionDropdown = divisions;
        }


        AppKpiMaster viewModel = null;

        if (id.HasValue)
        {
            viewModel = await context.AppKpiMasters.FirstOrDefaultAsync(a => a.ID == id);
        }

        return View(viewModel);
    }
    else
    {
        return RedirectToAction("Login", "User");
    }



}
