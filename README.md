 <div class="col-md-10">
     <form method="get" action="@Url.Action("Interruption", "PS")"
           class="d-flex align-items-center gap-2 flex-wrap">

         <select class="form-control form-control-sm" name="SourceSearch" style="width:20%;">
             <i class="fas fa-search" aria-hidden="true"></i>
             <option value="">Search Source</option>
             @foreach (var item in SourceDropdown)
             {

                 <option value="@item.ID.ToString()" selected="@(item.ID.ToString() == ViewBag.SourceSearch ? "selected" : null)">@item.SourceName</option>
             }
         </select>

         <select class="form-control form-control-sm" name="FeederSearch" style="width:20%;">
             <option value="">Search Feeder</option>
             @foreach (var item in feederDropdown)
             {
                 <option value="@item.ID.ToString()" selected="@(item.ID.ToString() == ViewBag.FeederSearch ? "selected" : null)">@item.FeederName</option>
             }
         </select>

         <select class="form-control form-control-sm" name="DTRSearch" style="width:20%;">
             <option value="">Search DTR</option>
             @foreach (var item in DTRDropdown)
             {
                 <option value="@item.ID.ToString()" selected="@(item.ID.ToString() == ViewBag.DTRSearch ? "selected" : null)">@item.DTRName</option>
             }
         </select>

         <button type="submit" class="btn btn-primary px-4 m-0">
             Search
         </button>
     </form>
 </div>



                using (var connection = new SqlConnection(GetConnection()))
                {
                    string sqlQuery = @"
SELECT 
    It.ID,
    sm.SourceName,
    fm.FeederName,
    Dm.DTRName,
    It.DTRCapacity,
    It.SourceID,
    It.FeederID,
    It.DTRID,
    It.NoOfConsumer,
    It.TypeOfInterruption,
   It.FromDate,
   It.ToDate
FROM App_Interruption It
LEFT JOIN App_SourceMaster sm ON It.SourceID = sm.ID
LEFT JOIN App_FeederMaster fm ON It.FeederID = fm.ID
LEFT JOIN App_DTRMaster Dm ON It.DTRID = Dm.ID
WHERE
    (@search IS NULL OR It.SourceID LIKE '%' + @search + '%' 
                      OR It.FeederID LIKE '%' + @search2 + '%' 
                      OR It.DTRID LIKE '%' + @search3 + '%')
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

                    var pagedData = await connection.QueryAsync<InterruptionListDTO>(sqlQuery, new
                    {
                        search = string.IsNullOrEmpty(SourceSearch) ? null : SourceSearch,
                        search2 = string.IsNullOrEmpty(FeederSearch) ? null : FeederSearch,
                        search3 = string.IsNullOrEmpty(DTRSearch) ? null : DTRSearch,
                        skip,
                        take = pageSize
                    });

                    var totalCount = await connection.ExecuteScalarAsync<int>(countQuery, new
                    {
                        search = string.IsNullOrEmpty(SourceSearch) ? null : SourceSearch,
                        search2 = string.IsNullOrEmpty(FeederSearch) ? null : FeederSearch,
                        search3 = string.IsNullOrEmpty(DTRSearch) ? null : DTRSearch,
                    });

                    ViewBag.ListData2 = pagedData.ToList();
                    ViewBag.CurrentPage = page;
                    ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
                    ViewBag.SourceSearch = SourceSearch;
                    ViewBag.FeederSearch = FeederSearch;
                    ViewBag.DTRSearch = DTRSearch;

                    ViewBag.Source = GetSourceDD();
                    ViewBag.feeder = GetFeederDD();
                    ViewBag.DTR = GetDTRDD();
                }
