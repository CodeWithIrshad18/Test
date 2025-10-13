this is for my grid 

    <div class="card card-custom">
        <div class="card-header-custom">KPI List</div>
        <div class="card-body p-0">
            <table class="table table-hover mb-0">
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
                                <td>@item.PeriodicityID</td>
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
                            <td colspan="3" class="text-center text-muted py-3">No data available</td>
                        </tr>
                    }
                </tbody>
            </table>
        </div>	


and this is my query to show data in grid 
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


but in place of this I want raw queries to fetch data so that I can easily join the table because I have some ids in table
