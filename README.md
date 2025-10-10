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
