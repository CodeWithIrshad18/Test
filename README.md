<div class="text-center">
    @if (ViewBag.TotalPages > 1)
    {
        <nav aria-label="Page navigation" style="font-size:12px;" class="d-flex justify-content-center">
            <ul class="pagination">

                <!-- First Page -->
                <li class="page-item @(ViewBag.CurrentPage == 1 ? "disabled" : "")">
                    <a class="page-link"
                       href="?page=1&searchString=@ViewBag.searchString&Dept=@ViewBag.Dept&KPI=@ViewBag.KPI">
                        « First
                    </a>
                </li>

                <!-- Previous -->
                <li class="page-item @(ViewBag.CurrentPage == 1 ? "disabled" : "")">
                    <a class="page-link"
                       href="?page=@(ViewBag.CurrentPage - 1)&searchString=@ViewBag.searchString&Dept=@ViewBag.Dept&KPI=@ViewBag.KPI">
                        ‹ Prev
                    </a>
                </li>

                <!-- Dynamic Page Numbers (one before and one after current) -->
                @for (int i = Math.Max(1, ViewBag.CurrentPage - 1); i <= Math.Min(ViewBag.CurrentPage + 1, ViewBag.TotalPages); i++)
                {
                    <li class="page-item @(ViewBag.CurrentPage == i ? "active" : "")">
                        <a class="page-link"
                           href="?page=@i&searchString=@ViewBag.searchString&Dept=@ViewBag.Dept&KPI=@ViewBag.KPI">
                            @i
                        </a>
                    </li>
                }

                <!-- Next -->
                <li class="page-item @(ViewBag.CurrentPage == ViewBag.TotalPages ? "disabled" : "")">
                    <a class="page-link"
                       href="?page=@(ViewBag.CurrentPage + 1)&searchString=@ViewBag.searchString&Dept=@ViewBag.Dept&KPI=@ViewBag.KPI">
                        Next ›
                    </a>
                </li>

                <!-- Last Page -->
                <li class="page-item @(ViewBag.CurrentPage == ViewBag.TotalPages ? "disabled" : "")">
                    <a class="page-link"
                       href="?page=@ViewBag.TotalPages&searchString=@ViewBag.searchString&Dept=@ViewBag.Dept&KPI=@ViewBag.KPI">
                        Last »
                    </a>
                </li>

            </ul>
        </nav>
    }
</div>

 
 
 
 
 
 <div class="card-footer text-center">
     @if (ViewBag.TotalPages > 1)
     {
         <nav>
             <ul class="pagination justify-content-center mb-0" style="font-size: 13px;">
                 <li class="page-item @(ViewBag.CurrentPage == 1 ? "disabled" : "")">
                     <a class="page-link" href="?page=@(ViewBag.CurrentPage - 1)&searchString=@ViewBag.searchString&Dept=@ViewBag.Dept&KPI=@ViewBag.KPI">Previous</a>
                 </li>

                 @for (int i = 1; i <= 5; i++)
                 {
                     <li class="page-item @(ViewBag.CurrentPage == i ? "active" : "")">
                         <a class="page-link" href="?page=@i&searchString=@ViewBag.searchString&Dept=@ViewBag.Dep&KPI=@ViewBag.KPIt">@i</a>
                     </li>
                 }

                 <li class="page-item @(ViewBag.CurrentPage == ViewBag.TotalPages ? "disabled" : "")">
                     <a class="page-link" href="?page=@(ViewBag.CurrentPage + 1)&searchString=@ViewBag.searchString&Dept=@ViewBag.Dept&KPI=@ViewBag.KPI">Next</a>
                 </li>
             </ul>
         </nav>
     }
 </div>




 <div class="text-center">


     @if (ViewBag.TotalPages > 1)
     {
         <nav aria-label="Page navigation" style="font-size:12px;" class="d-flex justify-content-center">
             <ul class="pagination">

                 <li class="page-item @(ViewBag.CurrentPage == 1 ? "disabled" : "")">
                     <a class="page-link" asp-action="PositionMaster"
                        asp-route-page="@(ViewBag.CurrentPage - 1)"
                        asp-route-SearchValue="@ViewBag.SearchValue"
                        asp-route-PositionId="@ViewBag.PositionId"
                     asp-route-WorksiteName="@ViewBag.WorksiteName">
                         Previous
                     </a>
                 </li>


                 @for (int i = Math.Max(1, ViewBag.CurrentPage - 1); i <= Math.Min(ViewBag.CurrentPage + 1, ViewBag.TotalPages); i++)
                 {
                     <li class="page-item @(ViewBag.CurrentPage == i ? "active" : "")">
                         <a class="page-link" asp-action="PositionMaster"
                            asp-route-page="@i"
                            asp-route-SearchValue="@ViewBag.SearchValue"
                             asp-route-PositionId="@ViewBag.PositionId"
                         asp-route-WorksiteName="@ViewBag.WorksiteName">
                             @i
                         </a>
                     </li>
                 }


                 <li class="page-item @(ViewBag.CurrentPage == ViewBag.TotalPages ? "disabled" : "")">
                     <a class="page-link" asp-action="PositionMaster"
                        asp-route-page="@(ViewBag.CurrentPage + 1)"
                        asp-route-SearchValue="@ViewBag.SearchValue"
                         asp-route-PositionId="@ViewBag.PositionId"
                     asp-route-WorksiteName="@ViewBag.WorksiteName">
                         Next
                     </a>
                 </li>
             </ul>
         </nav>
     }

 </div>
