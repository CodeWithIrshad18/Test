this is my controller code      
   [Authorize(Policy = "CanRead")]
        public async Task<IActionResult> ActualKPI(Guid? id, int page = 1, string searchString = "", string Dept = "", string KPI = "")
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


                int pageSize = 4;
                int skip = (page - 1) * pageSize;

                using (var connection = new SqlConnection(GetSAPConnectionString()))
                {
                    string sqlQuery = @"
select KI.ID,ki.Company,KI.Division,KI.Department,KI.Section,KI.ID as KPIID,pm.PeriodicityName,
KI.KPIDetails,KI.UnitID,SF.FinYear,KI.CreatedBy,KI.KPICode,KI.PeriodicityID,
TR.BaseLine,TR.Target,TR.BenchMarkPatner,TR.BenchMarkValue,UM.UnitCode,KI.NoofDecimal from App_KPIMaster_NOPR KI 
left Join App_UOM_NOPR UM on KI.UnitID = UM.ID 
left Join App_PeriodicityMaster_NOPR pm on KI.PeriodicityID = pm.ID 
left join App_TargetSetting_NOPR TR on TR.KPIID =KI.ID and TR.FinYearID='858c5bf6-2548-4bbe-a7e1-20a159910260'
Left join App_Sys_FinYear SF on TR.FinYearID = SF.ID;";

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

                return View();
            }
            else
            {
                return RedirectToAction("AcessDenied", "TPR");
            }
            }

this is my grid with pagination 

    <div class="card card-custom">
        <div class="card-header-custom">Actual against KPI List</div>
        <div class="card-body p-0">
           <table class="table table-hover mb-0">
    <thead>
        <tr>
            <th>KPI</th> 
            <th>UOM</th> 
            <th>Division</th>
            <th>Department</th>
             <th>Section</th>
             <th>Periodicity</th>
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
   <a href="#"
   class="refNoLink"
   data-id="@item.ID"
   data-KPICode="@item.KPICode"
   data-KPIID="@item.KPIID"
    data-Company="@item.Company"
   data-Division="@item.Division"
   data-Department="@item.Department"
   data-Section="@item.Section"
   data-UnitCode="@item.UnitCode"
   data-KPIDetails="@item.KPIDetails"
   data-PeriodicityID="@item.PeriodicityID" 
   data-FinYear="@item.FinYear" 
   data-PeriodicityName="@item.PeriodicityName">
   @item.KPIDetails
</a>

</td>
                   
                    <td>@item.UnitCode</td>
                     <td>@item.Division</td>
                    <td>@item.Department</td>
                    <td>@item.Section</td>
                    <td>@item.PeriodicityName</td>  
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

        </div>

                    <div class="card-footer text-center">
            @if (ViewBag.TotalPages > 1)
            {
                <nav>
                    <ul class="pagination justify-content-center mb-0" style="font-size: 13px;">
                        <li class="page-item @(ViewBag.CurrentPage == 1 ? "disabled" : "")">
                            <a class="page-link" href="?page=@(ViewBag.CurrentPage - 1)&searchString=@ViewBag.searchString&KPI=@ViewBag.KPI">Previous</a>
                        </li>

                        @for (int i = 1; i <= 5; i++)
                        {
                            <li class="page-item @(ViewBag.CurrentPage == i ? "active" : "")">
                                <a class="page-link" href="?page=@i&searchString=@ViewBag.searchString&KPI=@ViewBag.KPI">@i</a>
                            </li>
                        }

                        <li class="page-item @(ViewBag.CurrentPage == ViewBag.TotalPages ? "disabled" : "")">
                            <a class="page-link" href="?page=@(ViewBag.CurrentPage + 1)&KPI=@ViewBag.searchString&searchString=@ViewBag.KPI">Next</a>
                        </li>
                    </ul>
                </nav>
            }
        </div>
    </div>

it shows full records in one page , i want only 5 records in each page and also when i click on refNolink i want to highlight that which is selected
