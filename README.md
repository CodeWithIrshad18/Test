controller code 
        [Authorize(Policy = "CanRead")]
        public async Task<IActionResult> ActualKPI(Guid? id, int page = 1, string searchString = "", string Dept = "", string KPI = "",string FinyearID= "",string Div="")
        {

            if (HttpContext.Session.GetString("Session") != null)
            {
                var UserId = HttpContext.Session.GetString("Session");
                ViewBag.user = User;

                if (string.IsNullOrEmpty(UserId))
                    return RedirectToAction("AccessDenied", "TPR");

                string formName = "ActualKPI";
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
    KI.ID AS KPIID,
    KD.ID,
    KD.Value,
 KD.Hold,
    KD.HoldReason,
KD.PeriodTransactionID,
    KI.Company,
    TR.FinYearID,
    KI.Division,
    TR.ID AS TSID,
    KID.TypeofKPI,
    KI.Department,
    KI.Section,
    pm.PeriodicityName,
    KI.KPIDetails,
    KI.UnitID,
    SF.FinYear,
    KI.CreatedBy,
    KI.KPICode,
    KI.PeriodicityID,
    TR.BaseLine,
    TR.Target,
    TR.BenchMarkPatner,
    TR.BenchMarkValue,
    UM.UnitCode,
    KI.NoofDecimal
FROM App_KPIMaster_NOPR KI
LEFT JOIN App_UOM_NOPR UM ON KI.UnitID = UM.ID
LEFT JOIN App_TypeofKPI_NOPR KID ON KI.TypeofKPIID = KID.ID
LEFT JOIN App_PeriodicityMaster_NOPR pm ON KI.PeriodicityID = pm.ID
LEFT JOIN (
    SELECT 
        k1.*
    FROM App_KPIDetails_NOPR k1
    INNER JOIN (
        SELECT KPIID, MAX(CreatedOn) AS MaxCreatedOn
        FROM App_KPIDetails_NOPR
        GROUP BY KPIID
    ) k2 ON k1.KPIID = k2.KPIID AND k1.CreatedOn = k2.MaxCreatedOn
) KD ON KD.KPIID = KI.ID
LEFT JOIN App_TargetSetting_NOPR TR ON TR.KPIID = KI.ID
LEFT JOIN App_Sys_FinYear SF ON TR.FinYearID = SF.ID
WHERE
    KI.KPISPOC = @UserId 
    AND (KI.Deactivate IS NULL OR KI.Deactivate = 0)
    AND (@search IS NULL OR KI.KPICode LIKE '%' + @search + '%' OR UM.UnitCode LIKE '%' + @search + '%')
    AND (@search2 IS NULL OR KI.Department LIKE '%' + @search2 + '%')
    AND (@search1 IS NULL OR KI.Division LIKE '%' + @search1 + '%')
    AND (@search3 IS NULL OR KI.KPIDetails LIKE '%' + @search3 + '%')
AND (@search4 IS NULL OR TR.FinYearID LIKE '%' + @search4 + '%')
ORDER BY KI.KPIDetails DESC
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;
";

                    string countQuery = @"
        SELECT COUNT(*) 
FROM App_KPIMaster_NOPR k
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
LEFT JOIN App_TargetSetting_NOPR TR ON TR.KPIID = K.ID
WHERE
k.KPISPOC =@UserId AND k.Deactivate is null AND
    (@search IS NULL OR k.KPICode LIKE '%' + @search + '%' OR u.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%')
AND (@search1 IS NULL OR K.Division LIKE '%' + @search1 + '%')
AND (@search3 IS NULL OR k.KPIDetails LIKE '%' + @search3 + '%')
AND (@search4 IS NULL OR TR.FinYearID LIKE '%' + @search4 + '%');
    ";

                    var pagedData = await connection.QueryAsync<KpiListDto>(sqlQuery, new
                    {
                        UserId,
                        search = string.IsNullOrEmpty(searchString) ? null : searchString,
                        search1 = string.IsNullOrEmpty(Div) ? null : Div,
                        search2 = string.IsNullOrEmpty(Dept) ? null : Dept,
                        search3 = string.IsNullOrEmpty(KPI) ? null : KPI,
                        search4 = string.IsNullOrEmpty(FinyearID) ? null : FinyearID,
                        skip,
                        take = pageSize
                    });

                    var totalCount = await connection.ExecuteScalarAsync<int>(countQuery, new
                    {
                        UserId,
                        search = string.IsNullOrEmpty(searchString) ? null : searchString,
                        search1 = string.IsNullOrEmpty(Div) ? null : Div,
                        search2 = string.IsNullOrEmpty(Dept) ? null : Dept,
                        search3 = string.IsNullOrEmpty(KPI) ? null : KPI,
                        search4 = string.IsNullOrEmpty(FinyearID) ? null : FinyearID
                    });

                    ViewBag.ListData2 = pagedData.ToList();
                    ViewBag.CurrentPage = page;
                    ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
                    ViewBag.searchString = searchString;
                    ViewBag.Dept = Dept;
                    ViewBag.Div = Div;
                    ViewBag.KPI = KPI;
                    ViewBag.FinyearID = FinyearID;
                  
                }


                ViewBag.department = GetDept();
                ViewBag.FinYear = GetFinYearDD();
                ViewBag.division = GetDivisionDD();
                var finYears = GetFinYearDD();
                var currentFY = GetCurrentFinYear();

                ViewBag.FinYearDropdown = finYears;
                ViewBag.CurrentFY = currentFY;

                return View();
            }
            else
            {
                return RedirectToAction("AcessDenied", "TPR");
            }
        }

and this is my grid and pagination 
    <div class="card card-custom">
        <div class="card-header-custom">Actual against KPI List</div>
        <div class="card-body p-0">
           <table class="table table-hover mb-0">
    <thead>
        <tr>
            <th width="9%;">KPI Code</th>
            <th width="20%;">KPI Details</th> 
            <th>UOM</th> 
            <th>Division</th>
            <th>Department</th>
             <th>Section</th>
            
        </tr>
    </thead>
    <tbody>
        @if (ViewBag.ListData2 != null)
        {
            @foreach (var item in ViewBag.ListData2)
            {
                <tr>
                    <td>
  <a href="#"
   class="refNoLink"
   data-kpiid="@item.KPIID"
   data-id="@item.ID"
   data-kpicode="@item.KPICode"
   data-periodtransactionid="@item.PeriodTransactionID"
   data-hold="@item.Hold"
   data-holdreason="@item.HoldReason"
   data-tsid="@item.TSID"
   data-company="@item.Company"
   data-division="@item.Division"
   data-TypeofKPI="@item.TypeofKPI"
   data-department="@item.Department"
   data-section="@item.Section"
   data-unitcode="@item.UnitCode"
   data-kpidetails="@item.KPIDetails"
   data-periodicityid="@item.PeriodicityID"
   data-finyear="@item.FinYear"
   data-finyearid="@item.FinYearID"
   data-periodicityname="@item.PeriodicityName">
   @item.KPICode
</a>

</td>
                   
                <td>@item.KPIDetails</td>
                    <td>@item.UnitCode</td>
                     <td>@item.Division</td>
                    <td>@item.Department</td>
                    <td>@item.Section</td>  
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

        </div>

       <div class="text-center mt-3">
    @if (ViewBag.TotalPages > 1)
    {
        <nav aria-label="Page navigation" style="font-size:12px;" class="d-flex justify-content-center">
            <ul class="pagination">


                <li class="page-item @(ViewBag.CurrentPage == 1 ? "disabled" : "")">
                    <a class="page-link"
                       href="?page=1&searchString=@ViewBag.searchString&Dept=@ViewBag.Dept&KPI=@ViewBag.KPI&Div=@ViewBag.Div">
                        « First
                    </a>
                </li>

                <li class="page-item @(ViewBag.CurrentPage == 1 ? "disabled" : "")">
                    <a class="page-link"
                       href="?page=@(ViewBag.CurrentPage - 1)&searchString=@ViewBag.searchString&Dept=@ViewBag.Dept&KPI=@ViewBag.KPI&Div=@ViewBag.Div">
                        ‹ Prev
                    </a>
                </li>

              
                @for (int i = Math.Max(1, ViewBag.CurrentPage - 1); i <= Math.Min(ViewBag.CurrentPage + 1, ViewBag.TotalPages); i++)
                {
                    <li class="page-item @(ViewBag.CurrentPage == i ? "active" : "")">
                        <a class="page-link"
                           href="?page=@i&searchString=@ViewBag.searchString&Dept=@ViewBag.Dept&KPI=@ViewBag.KPI&Div=@ViewBag.Div">
                            @i
                        </a>
                    </li>
                }

 
                <li class="page-item @(ViewBag.CurrentPage == ViewBag.TotalPages ? "disabled" : "")">
                    <a class="page-link"
                       href="?page=@(ViewBag.CurrentPage + 1)&searchString=@ViewBag.searchString&Dept=@ViewBag.Dept&KPI=@ViewBag.KPI&Div=@ViewBag.Div">
                        Next ›
                    </a>
                </li>

  
                <li class="page-item @(ViewBag.CurrentPage == ViewBag.TotalPages ? "disabled" : "")">
                    <a class="page-link"
                       href="?page=@ViewBag.TotalPages&searchString=@ViewBag.searchString&Dept=@ViewBag.Dept&KPI=@ViewBag.KPI&Div=@ViewBag.Div">
                        Last »
                    </a>
                </li>

            </ul>
        </nav>
    }
</div>
    </div>

why pagination is not showing?
