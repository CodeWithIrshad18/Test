int pageSize = 5;
int skip = (page - 1) * pageSize;

using (var connection = new SqlConnection(GetSAPConnectionString()))
{
    string sqlQuery = @"
        SELECT 
            k.ID,
            k.KPIDetails,
            ps.Perspectives,
            u.UnitCode,
            k.KPILevel,
            p.PeriodicityName,
            k.Division,
            k.Department,
            g.Name,
            k.KPICode
        FROM App_KPIMaster_NOPR k
        LEFT JOIN App_PeriodicityMaster_NOPR p ON k.PeriodicityID = p.ID
        LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
        LEFT JOIN App_Prespectives_NOPR ps ON k.PerspectiveID = ps.ID
        LEFT JOIN App_TypeofKPI_NOPR t ON k.TypeofKPIID = t.ID
        LEFT JOIN App_GoodPerformance_NOPR g ON k.GoodPerformance = g.ID
        WHERE (@search IS NULL OR k.KPIDetails LIKE '%' + @search + '%' 
                            OR k.KPILevel LIKE '%' + @search + '%'
                            OR ps.Perspectives LIKE '%' + @search + '%'
                            OR u.UnitCode LIKE '%' + @search + '%')
        ORDER BY k.KPIDetails
        OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;
    ";

    string countQuery = @"
        SELECT COUNT(*) 
        FROM App_KPIMaster_NOPR k
        LEFT JOIN App_Prespectives_NOPR ps ON k.PerspectiveID = ps.ID
        LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
        WHERE (@search IS NULL OR k.KPIDetails LIKE '%' + @search + '%'
                            OR k.KPILevel LIKE '%' + @search + '%'
                            OR ps.Perspectives LIKE '%' + @search + '%'
                            OR u.UnitCode LIKE '%' + @search + '%');
    ";

    var pagedData = await connection.QueryAsync<KpiListDto>(sqlQuery, new
    {
        search = string.IsNullOrEmpty(searchString) ? null : searchString,
        skip,
        take = pageSize
    });

    var totalCount = await connection.ExecuteScalarAsync<int>(countQuery, new
    {
        search = string.IsNullOrEmpty(searchString) ? null : searchString
    });

    ViewBag.ListData2 = pagedData.ToList();
    ViewBag.CurrentPage = page;
    ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
    ViewBag.searchString = searchString;
}

<table class="table table-hover mb-0">
    <thead>
        <tr>
            <th>KPI</th>
            <th>Area</th>
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
                    <td>@item.KPIDetails</td>
                    <td>@item.Perspectives</td>
                    <td>@item.UnitCode</td>
                    <td>@item.KPILevel</td>
                    <td>@item.PeriodicityName</td>
                    <td>@item.Division</td>
                    <td>@item.Department</td>
                    <td>@item.Name</td>
                    <td>@item.KPICode</td>
                </tr>
            }
        }
        else
        {
            <tr>
                <td colspan="9" class="text-center text-muted py-3">No data available</td>
            </tr>
        }
    </tbody>
</table>

public class KpiListDto
{
    public Guid ID { get; set; }
    public string? KPIDetails { get; set; }       // KPI
    public string? Perspectives { get; set; }     // Area
    public string? UnitCode { get; set; }         // UOM
    public string? KPILevel { get; set; }
    public string? PeriodicityName { get; set; }
    public string? Division { get; set; }
    public string? Department { get; set; }
    public string? Name { get; set; }             // Good Performance Name
    public string? KPICode { get; set; }
}

<td>
    <a href="#" 
       class="refNoLink"
       data-id="@item.ID"
       data-KPIDetails="@item.KPIDetails"
       data-KPILevel="@item.KPILevel"
       data-Perspectives="@item.Perspectives"
       data-UnitCode="@item.UnitCode"
       data-PeriodicityName="@item.PeriodicityName"
       data-Division="@item.Division"
       data-Department="@item.Department"
       data-GoodPerformance="@item.Name"
       data-KPICode="@item.KPICode">
       @item.KPIDetails
    </a>
</td>



I have this main model 

public partial class AppKpiMaster
{
    public Guid ID { get; set; }
    public string? Company { get; set; }
    public string? Division { get; set; }
    public string? Department { get; set; }
    public string? Section { get; set; }
    public string? KPICode { get; set; }
    public string? KPIDetails { get; set; }
    public Guid UnitID { get; set; }
    public string? CreatedBy { get; set; }
    public Guid? PeriodicityID { get; set; }
    public string? GoodPerformance { get; set; }
    public decimal? HistoricalBest { get; set; }
    public Guid? HistoricalBestYear { get; set; }
    public decimal? TheoreticalBest { get; set; }
    public int? NoofDecimal { get; set; }
    public string? KPIDefination { get; set; }
    public Guid? PerspectiveID { get; set; }
    public Guid? TypeofKPIID { get; set; }
    public Guid? LTPSTPID { get; set; }
    public string? Central_Local { get; set; }
    public string? KPIUpto { get; set; }
    public bool? Deactivate { get; set; }
    public DateTime? DeactivateOn { get; set; }
    public string? KPILevel { get; set; }
    public string? KPIMode { get; set; }
    public string? SourceData { get; set; }
    public string? COMode { get; set; }
    public string? PCNCode { get; set; }

}

and these are for joining 
 public class AppBasisOfTarget
 {
     public Guid ID { get; set; }
}

 public class AppPeriodicityMaster
 {

     public Guid ID { get; set; }
     public string? PeriodicityName { get; set; }

}
 public class AppPerspectives
 {
     public Guid ID { get; set; }
     public string? Perspectives { get; set; }
}
 public class AppUOM
 {
     public Guid ID { get; set; }
     public string? UnitCode { get; set; }
}
  public class AppTypeOfKPI
  {
      public Guid ID { get; set; }
      public string? TypeofKPI { get; set; }
     
  }

this Is my controller 
        public async Task<IActionResult> CreateKPI(Guid? id, int page = 1, string searchString = "")
        {
            if (HttpContext.Session.GetString("Session") != null)
            {
                var UserId = HttpContext.Session.GetString("Session");
                ViewBag.user = User;

                if (string.IsNullOrEmpty(UserId))
                    return RedirectToAction("AccessDenied", "TPR");

                string formName = "CreateKPI";
                var form = await context.AppFormDetails
                    .Where(f => f.FormName == formName)
                    .Select(f => f.Id)
                    .FirstOrDefaultAsync();

                if (form == default)
                    return RedirectToAction("AccessDenied", "TPR");

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
                int skip = (page - 1) * pageSize;

                using (var connection = new SqlConnection(GetSAPConnectionString()))
                {
                    string sqlQuery = @"
                SELECT 
    k.ID,
    k.KPIDetails,
    k.KPILevel,
    p.PeriodicityName,
    u.UnitCode,
    ps.Perspectives,
    t.TypeofKPI,
    k.Division,
    k.Department,
    g.Name,
    k.KPICode
               FROM App_KPIMaster_NOPR k
LEFT JOIN App_PeriodicityMaster_NOPR p ON k.PeriodicityID = p.ID
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
LEFT JOIN App_Prespectives_NOPR ps ON k.PerspectiveID = ps.ID
LEFT JOIN App_TypeofKPI_NOPR t ON k.TypeofKPIID = t.ID
LEFT JOIN App_GoodPerformance_NOPR g ON k.GoodPerformance = g.ID
                --WHERE (@search IS NULL OR k.KPILevel LIKE '%' + @search + '%')
                ORDER BY k.KPIDetails
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;";

                    string countQuery = @"
                SELECT COUNT(*) 
                FROM App_KPIMaster_NOPR k
                WHERE (@search IS NULL OR k.KPILevel LIKE '%' + @search + '%');
            ";

                    var pagedData = await connection.QueryAsync<KpiListDto>(sqlQuery, new
                    {
                        search = string.IsNullOrEmpty(searchString) ? null : searchString,
                        skip,
                        take = pageSize
                    });

                    var totalCount = await connection.ExecuteScalarAsync<int>(countQuery, new
                    {
                        search = string.IsNullOrEmpty(searchString) ? null : searchString
                    });

                    ViewBag.ListData2 = pagedData.ToList();
                    ViewBag.CurrentPage = page;
                    ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
                    ViewBag.searchString = searchString;
                }

               
                ViewBag.Area = GetAreaDD();
                ViewBag.Type = GetKPITypeDD();
                ViewBag.Unit = GetUnitDD();
                ViewBag.Periodicity = GetPeriodicityDD();
                ViewBag.perforamnce = GetPerformanceDD();
                ViewBag.division = GetDivisionDD();

              
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

and this is my viewside 
                    @if (ViewBag.ListData2 != null)
                    {
                        @foreach (var item in ViewBag.ListData2)
                        {
                            <tr>
                                <td>
                                   <a href="#" class="refNoLink"
   data-id="@item.ID"
   data-KPIDetails="@item.KPIDetails"
   data-PerspectiveID="@item.PerspectiveID"
   data-UnitID="@item.UnitID"
   data-PeriodicityID="@item.PeriodicityID"
                                   data-Division="@item.Division"
                                   data-Department="@item.Department"
                                   data-GoodPerformance="@item.GoodPerformance"
                                   data-KPICode="@item.KPICode"
                                   data-KPILevel="@item.KPILevel"
                                   data-KPIDefination="@item.KPIDefination"
                                   data-NoofDecimal="@item.NoofDecimal"
                                   data-Company="@item.Company"
                                   data-TypeofKPIID="@item.TypeofKPIID">
    @item.KPIDetails
</a>
                                </td>
                                <td>@item.KPIDetails</td>  
                                <td>@item.UnitID</td>
                                <td>@item.KPILevel</td>
                                <td>@item.Division</td>
                                <td>@item.Department</td>
                                <td>@item.Name</td>
                                <td>@item.KPICode</td>
                            </tr>
                        }
                    }
                    else
                    {
                        <tr>
                            <td colspan="3" class="text-center text-muted py-3">No data available</td>
                        </tr>
                    }

and I want to show grid like this 
KPI	Area	UOM	KPI Level	Periodicity	Division	Department	Good Performance	KPI Code
