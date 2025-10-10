<div class="card-footer text-center">
    @if (ViewBag.TotalPages > 1)
    {
        int totalPages = ViewBag.TotalPages;
        int currentPage = ViewBag.CurrentPage;
        string searchString = ViewBag.UOM ?? "";

        // Define the window size (number of visible pages)
        int maxPagesToShow = 5;

        // Calculate the start and end pages
        int startPage = Math.Max(1, currentPage - (maxPagesToShow / 2));
        int endPage = Math.Min(totalPages, startPage + maxPagesToShow - 1);

        // Adjust startPage if near the end
        if (endPage - startPage < maxPagesToShow - 1)
        {
            startPage = Math.Max(1, endPage - maxPagesToShow + 1);
        }

        <nav>
            <ul class="pagination justify-content-center mb-0" style="font-size: 13px;">

                <!-- Previous Button -->
                <li class="page-item @(currentPage == 1 ? "disabled" : "")">
                    <a class="page-link" href="?page=@(currentPage - 1)&searchString=@searchString">Previous</a>
                </li>

                <!-- First Page Link if needed -->
                @if (startPage > 1)
                {
                    <li class="page-item">
                        <a class="page-link" href="?page=1&searchString=@searchString">1</a>
                    </li>
                    @if (startPage > 2)
                    {
                        <li class="page-item disabled"><span class="page-link">...</span></li>
                    }
                }

                <!-- Page Number Links -->
                @for (int i = startPage; i <= endPage; i++)
                {
                    <li class="page-item @(currentPage == i ? "active" : "")">
                        <a class="page-link" href="?page=@i&searchString=@searchString">@i</a>
                    </li>
                }

                <!-- Last Page Link if needed -->
                @if (endPage < totalPages)
                {
                    @if (endPage < totalPages - 1)
                    {
                        <li class="page-item disabled"><span class="page-link">...</span></li>
                    }
                    <li class="page-item">
                        <a class="page-link" href="?page=@totalPages&searchString=@searchString">@totalPages</a>
                    </li>
                }

                <!-- Next Button -->
                <li class="page-item @(currentPage == totalPages ? "disabled" : "")">
                    <a class="page-link" href="?page=@(currentPage + 1)&searchString=@searchString">Next</a>
                </li>
            </ul>
        </nav>
    }
</div>



make this pagination show the page number 5 then show next , accordingly  
<div class="card-footer text-center">
     @if (ViewBag.TotalPages > 1)
     {
         <nav>
             <ul class="pagination justify-content-center mb-0" style="font-size: 13px;">
                 <li class="page-item @(ViewBag.CurrentPage == 1 ? "disabled" : "")">
                     <a class="page-link" href="?page=@(ViewBag.CurrentPage - 1)&searchString=@ViewBag.UOM">Previous</a>
                 </li>

                 @for (int i = 1; i <= 2; i++)
                 {
                     <li class="page-item @(ViewBag.CurrentPage == i ? "active" : "")">
                         <a class="page-link" href="?page=@i&searchString=@ViewBag.UOM">@i</a>
                     </li>
                 }

                 <li class="page-item @(ViewBag.CurrentPage == ViewBag.TotalPages ? "disabled" : "")">
                     <a class="page-link" href="?page=@(ViewBag.CurrentPage + 1)&searchString=@ViewBag.UOM">Next</a>
                 </li>
             </ul>
         </nav>
     }
 </div>
