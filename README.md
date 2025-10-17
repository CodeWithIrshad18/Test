[HttpGet]
public async Task<JsonResult> GetPeriodicity(Guid periodicityId)
{
    using (var connection = new SqlConnection(GetConnection()))
    {
        string query = @"
            SELECT PeriodicityName 
            FROM App_PeriodicityTransaction
            WHERE PeriodicityID = @PeriodicityID
            ORDER BY Sl_no";

        var result = await connection.QueryAsync<string>(query, new { PeriodicityID = periodicityId });

        return Json(result);
    }
}

<button type="button" 
        class="btn btn-sm btn-primary show-periodicity"
        data-periodicityid="@item.PeriodicityID"
        data-bs-toggle="modal"
        data-bs-target="#periodicityModal">
    View Periods
</button>
<div class="modal fade" id="periodicityModal" tabindex="-1" aria-labelledby="periodicityModalLabel" aria-hidden="true">
  <div class="modal-dialog modal-lg">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="periodicityModalLabel">Period Targets</h5>
        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
      </div>
      <div class="modal-body">
        <table class="table table-bordered" id="periodicityTable">
          <thead>
            <tr>
              <th>Period</th>
              <th>Target Value</th>
            </tr>
          </thead>
          <tbody>
            <!-- Filled dynamically -->
          </tbody>
        </table>
      </div>
    </div>
  </div>
</div>


<script>
document.addEventListener('DOMContentLoaded', function () {
    document.querySelectorAll('.show-periodicity').forEach(btn => {
        btn.addEventListener('click', function () {
            const periodicityId = this.dataset.periodicityid;
            const tableBody = document.querySelector('#periodicityTable tbody');
            tableBody.innerHTML = `<tr><td colspan="2">Loading...</td></tr>`;

            fetch(`/YourControllerName/GetPeriodicity?periodicityId=${periodicityId}`)
                .then(response => response.json())
                .then(data => {
                    tableBody.innerHTML = '';
                    if (data.length > 0) {
                        data.forEach(period => {
                            const row = `
                                <tr>
                                    <td>${period}</td>
                                    <td><input type="text" class="form-control form-control-sm" name="Target_${period}" placeholder="Enter target"></td>
                                </tr>
                            `;
                            tableBody.insertAdjacentHTML('beforeend', row);
                        });
                    } else {
                        tableBody.innerHTML = `<tr><td colspan="2">No periods found.</td></tr>`;
                    }
                })
                .catch(err => {
                    tableBody.innerHTML = `<tr><td colspan="2" class="text-danger">Error loading data.</td></tr>`;
                });
        });
    });
});
</script>
 
 
 

i have this query 

 

select PeriodicityName from App_PeriodicityTransaction where PeriodicityID ='D16070D5-69F0-402B-BA51-8D2909BECA11' order by Sl_no

this is my data-bs which has Id 

data-PeriodicityID="@item.PeriodicityID"

and this is data for Monthly

APR
MAY
JUN
JUL
AUG
SEP
OCT
NOV
DEC
JAN
FEB
MAR

and i have this controller code 
        [Authorize(Policy = "CanRead")]
        public async Task<IActionResult> TargetKPI(Guid? id, int page = 1, string searchString = "", string Dept = "", string KPI = "")
        {
            if (HttpContext.Session.GetString("Session") != null)
            {
                var UserId = HttpContext.Session.GetString("Session");
                ViewBag.user = User;

                if (string.IsNullOrEmpty(UserId))
                    return RedirectToAction("AccessDenied", "TPR");

                string formName = "TargetKPI";
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

                int pageSize = 4;
                int skip = (page - 1) * pageSize;

                using (var connection = new SqlConnection(GetSAPConnectionString()))
                {
                    string sqlQuery = @"
    SELECT 
    k.ID,
    k.KPIDetails,
    ps.Perspectives,
    u.UnitCode,
    k.UnitID,
    k.KPILevel,
    p.PeriodicityName,
    k.Division,
    k.Department,
    k.Section,
    g.Name,
    k.KPICode,
    k.TypeofKPIID,
    k.KPIDefination,
    k.Company,
    k.GoodPerformance,
    k.PeriodicityID,
    k.UnitID,
    k.PerspectiveID,
    k.NoofDecimal
FROM App_KPIMaster_NOPR k
LEFT JOIN App_PeriodicityMaster_NOPR p ON k.PeriodicityID = p.ID
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
LEFT JOIN App_Prespectives_NOPR ps ON k.PerspectiveID = ps.ID
LEFT JOIN App_TypeofKPI_NOPR t ON k.TypeofKPIID = t.ID
LEFT JOIN App_GoodPerformance_NOPR g ON k.GoodPerformance = g.ID
WHERE
    (@search IS NULL OR k.KPICode LIKE '%' + @search + '%' OR u.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%')
AND (@search3 IS NULL OR k.KPIDetails LIKE '%' + @search3 + '%')
ORDER BY k.KPIDetails
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;
    ";

                    string countQuery = @"
        SELECT COUNT(*) 
FROM App_KPIMaster_NOPR k
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
WHERE
    (@search IS NULL OR k.KPICode LIKE '%' + @search + '%' OR u.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%')
AND (@search3 IS NULL OR k.KPIDetails LIKE '%' + @search3 + '%');

    ";

                    var pagedData = await connection.QueryAsync<KpiListDto>(sqlQuery, new
                    {
                        search = string.IsNullOrEmpty(searchString) ? null : searchString,
                        search2 = string.IsNullOrEmpty(Dept) ? null : Dept,
                        search3 = string.IsNullOrEmpty(KPI) ? null : KPI,
                        skip,
                        take = pageSize
                    });

                    var totalCount = await connection.ExecuteScalarAsync<int>(countQuery, new
                    {
                        search = string.IsNullOrEmpty(searchString) ? null : searchString,
                        search2 = string.IsNullOrEmpty(Dept) ? null : Dept,
                        search3 = string.IsNullOrEmpty(KPI) ? null : KPI
                    });

                    ViewBag.ListData2 = pagedData.ToList();
                    ViewBag.CurrentPage = page;
                    ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
                    ViewBag.searchString = searchString;
                    ViewBag.Dept = Dept;
                    ViewBag.KPI = KPI;
                }



                ViewBag.FinYear = GetFinYearDD();

                var finYears = GetFinYearDD();
                var currentFY = GetCurrentFinYear();

                ViewBag.FinYearDropdown = finYears;
                ViewBag.CurrentFY = currentFY;




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

and this has quarterly, yearly and Monthly . i want like this reference image for period and target value
