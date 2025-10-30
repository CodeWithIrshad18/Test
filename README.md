SELECT DISTINCT
    k.ID,
    ts.*,
    k.KPIDetails,
    u.UnitCode,
    k.UnitID,
    p.PeriodicityName,
    k.KPICode,
    k.Department,
    k.Division,
    k.Section,
    k.PeriodicityID,
    k.UnitID,

    CASE 
        WHEN EXISTS (
            SELECT 1 
            FROM App_TargetSettingDetails_NOPR td 
            WHERE td.MasterId = ts.ID
        ) THEN 'Y'
        ELSE 'N'
    END AS TargetSetStatus

FROM App_KPIMaster_NOPR k
LEFT JOIN App_PeriodicityMaster_NOPR p ON k.PeriodicityID = p.ID
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
LEFT JOIN App_Prespectives_NOPR ps ON k.PerspectiveID = ps.ID
LEFT JOIN App_TypeofKPI_NOPR t ON k.TypeofKPIID = t.ID
LEFT JOIN App_GoodPerformance_NOPR g ON k.GoodPerformance = g.ID
LEFT JOIN App_TargetSetting_NOPR ts ON k.ID = ts.KPIID

WHERE
    (@search IS NULL OR k.KPICode LIKE '%' + @search + '%' OR u.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%')
AND (@search3 IS NULL OR k.KPIDetails LIKE '%' + @search3 + '%')

ORDER BY k.KPIDetails
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;

 public string TargetSetStatus { get; set; }
                   

<thead>
    <tr>
        <th>KPI</th> 
        <th>UOM</th> 
        <th>Division</th>
        <th>Department</th>
        <th>Section</th>
        <th>KPI Code</th>
        <th>Target Set</th>
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
                       data-TSID="@item.TSID"
                       data-Company="@item.Company"
                       data-Division="@item.Division"
                       data-Department="@item.Department"
                       data-Section="@item.Section"
                       data-UnitCode="@item.UnitCode"
                       data-KPIDetails="@item.KPIDetails"
                       data-PeriodicityID="@item.PeriodicityID" 
                       data-FinYear="@item.FinYear" 
                       data-FinYearID="@item.FinYearID" 
                       data-PeriodicityName="@item.PeriodicityName">
                        @item.KPIDetails
                    </a>
                </td>
                <td>@item.UnitCode</td>
                <td>@item.Division</td>
                <td>@item.Department</td>
                <td>@item.Section</td> 
                <td>@item.KPICode</td>
                <td>@item.TargetSetStatus</td>
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


                    
                    
                    string sqlQuery = @"
SELECT distinct
    k.ID,
    ts.*,
    k.KPIDetails,
    u.UnitCode,
    k.UnitID,
    p.PeriodicityName,
    k.KPICode,
    k.Department,
    k.Division,
    k.Section,
    k.PeriodicityID,
    k.UnitID
FROM App_KPIMaster_NOPR k
LEFT JOIN App_PeriodicityMaster_NOPR p ON k.PeriodicityID = p.ID
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
LEFT JOIN App_Prespectives_NOPR ps ON k.PerspectiveID = ps.ID
LEFT JOIN App_TypeofKPI_NOPR t ON k.TypeofKPIID = t.ID
LEFT JOIN App_GoodPerformance_NOPR g ON k.GoodPerformance = g.ID
LEFT JOIN App_TargetSetting_NOPR ts On K.ID = ts.KPIID
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

this is my query to combine in upper main query to add a new column Target set Y/N if data exist in this query then Y otherwise N and the MasterId is the ID of the App_TargetSetting_NOPR
select * from App_TargetSettingDetails_NOPR where MasterId ='c0f8da5f-6251-4a25-8e9a-e75b81d34379'


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
   data-TSID="@item.TSID"
    data-Company="@item.Company"
   data-Division="@item.Division"
   data-Department="@item.Department"
   data-Section="@item.Section"
   data-UnitCode="@item.UnitCode"
   data-KPIDetails="@item.KPIDetails"
   data-PeriodicityID="@item.PeriodicityID" 
   data-FinYear="@item.FinYear" 
   data-FinYearID="@item.FinYearID" 
   data-PeriodicityName="@item.PeriodicityName">
   @item.KPIDetails
</a>

</td>
                   
                    <td>@item.UnitCode</td>
                     <td>@item.Division</td>
                    <td>@item.Department</td>
                    <td>@item.Section</td> 
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
