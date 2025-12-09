public async Task<IActionResult> Interruption(Guid? id, int page = 1, string searchString = "")
{
    if (HttpContext.Session.GetString("Session") != null)
    {
        var UserId = HttpContext.Session.GetString("Session");
        ViewBag.user = User;

        int pageSize = 4;
        int skip = (page - 1) * pageSize;

        using (var connection = new SqlConnection(GetConnection()))
        {
            string sqlQuery = @"
SELECT 
    It.ID,
    sm.SourceName,
    fm.FeederName,
    Dm.DTRName,
    It.DTRCapacity,
    It.NoOfConsumer
FROM App_Interruption It
LEFT JOIN App_SourceMaster sm ON It.SourceID = sm.ID
LEFT JOIN App_FeederMaster fm ON It.FeederID = fm.ID
LEFT JOIN App_DTRMaster Dm ON It.DTRID = Dm.ID
WHERE
    (@search IS NULL OR sm.SourceName LIKE '%' + @search + '%' 
                      OR fm.FeederName LIKE '%' + @search + '%' 
                      OR Dm.DTRName LIKE '%' + @search + '%')
ORDER BY Dm.DTRName
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;
";

            string countQuery = @"
SELECT COUNT(*)  
FROM App_Interruption It
LEFT JOIN App_SourceMaster sm ON It.SourceID = sm.ID
LEFT JOIN App_FeederMaster fm ON It.FeederID = fm.ID
LEFT JOIN App_DTRMaster Dm ON It.DTRID = Dm.ID
WHERE
    (@search IS NULL OR sm.SourceName LIKE '%' + @search + '%' 
                      OR fm.FeederName LIKE '%' + @search + '%' 
                      OR Dm.DTRName LIKE '%' + @search + '%');
";

            var pagedData = await connection.QueryAsync<AppInterruption>(sqlQuery, new
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

        return View();
    }
    else
    {
        return RedirectToAction("Login", "User");
    }
}

<table class="table table-hover mb-0">
    <thead>
        <tr>
            <th>Source</th>
            <th>Feeder</th>
            <th>DTR Name</th>
            <th>DTR Capacity</th>
            <th>No Of Consumer</th>
        </tr>
    </thead>
    <tbody>
        @if (ViewBag.ListData2 != null)
        {
            foreach (var item in ViewBag.ListData2)
            {
                <tr>
                    <td>@item.SourceName</td>
                    <td>@item.FeederName</td>
                    <td>
                        <a href="#"
                           class="refNoLink"
                           data-id="@item.ID"
                           data-DTRName="@item.DTRName"
                           data-SourceName="@item.SourceName"
                           data-FeederName="@item.FeederName"
                           data-DTRCapacity="@item.DTRCapacity"
                           data-NoOfConsumer="@item.NoOfConsumer">
                            @item.DTRName
                        </a>
                    </td>
                    <td>@item.DTRCapacity</td>
                    <td>@item.NoOfConsumer</td>
                </tr>
            }
        }
        else
        {
            <tr>
                <td colspan="5" class="text-center text-muted py-3">No data available</td>
            </tr>
        }
    </tbody>
</table>


        
        
        
        public async Task<IActionResult> Interruption(Guid? id, int page = 1, string searchString = "")
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

                int pageSize = 4;
                int skip = (page - 1) * pageSize;

                using (var connection = new SqlConnection(GetConnection()))
                {
                    string sqlQuery = @"
        SELECT 
    k.ID,
    k.KPIDetails,
    k.KPISPOC,
    k.ImmediateSuperior,
    k.Deactivate,
    k.DeactivateFrom,
    k.DeactivateTo,
    k.HOD,
    ps.Perspectives,
    u.UnitCode,
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
AND (@search1 IS NULL OR k.Division LIKE '%' + @search1 + '%')
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
AND (@search1 IS NULL OR k.Division LIKE '%' + @search1 + '%')
AND (@search3 IS NULL OR k.KPIDetails LIKE '%' + @search3 + '%');

    ";

                    var pagedData = await connection.QueryAsync<AppInterruption>(sqlQuery, new
                    {
                        search = string.IsNullOrEmpty(searchString) ? null : searchString,
                        skip,
                        take = pageSize
                    });

                    var totalCount = await connection.ExecuteScalarAsync<int>(countQuery, new
                    {
                        search = string.IsNullOrEmpty(searchString) ? null : searchString,

                    });

                    ViewBag.ListData2 = pagedData.ToList();
                    ViewBag.CurrentPage = page;
                    ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
                    ViewBag.searchString = searchString;
                   
                }


              }
            else
            {
                return RedirectToAction("Login", "User");
            }
        }


   <table class="table table-hover mb-0">
       <thead>
           <tr>
               <th>DTR Name</th>


               <th>DTR Capacity</th>


               <th>No Of Consumer</th>
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
                              data-DTRName="@item.DTRID" data-SourceID="@item.SourceID" data-FeederID="@item.FeederID" data-DTRCapacity="@item.DTRCapacity" data-NoOfConsumer="@item.NoOfConsumer" data-createdBy="@item.CreatedBy">
                               @item.DTRID
                           </a>
                       </td>

                       <td>
                           @item.DTRCapacity
                       </td>
                       <td>
                           @item.NoOfConsumer
                       </td>

                   </tr>
               }
           }
           else
           {
               <tr>
                   <td colspan="3" class="text-center text-muted py-3">No data available</td>
               </tr>
           }
       </tbody>
   </table>


New query 
 select It.ID,sm.SourceName,fm.FeederName,Dm.DTRName,It.DTRCapacity,It.NoOfConsumer from App_Interruption It
 Left Join App_SourceMaster sm
 on It.SourceID = sm.ID
 Left Join App_FeederMaster fm
on It.FeederID = fm.ID
LEFT JOIN App_DTRMaster Dm
on It.DTRID = Dm.ID
