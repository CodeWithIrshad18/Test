i want like this pagination 

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
