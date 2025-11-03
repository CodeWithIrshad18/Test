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
