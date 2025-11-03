<tbody>
    @if (ViewBag.ListData2 != null)
    {
        foreach (var item in ViewBag.ListData2)
        {
            <tr>
                <td>@item.UserID</td>
                <td>@item.ema_ename</td>
                <td>@item.Description</td>
                <td>@Html.Raw(item.AllowRead == true
                    ? "<i class='bi bi-check-circle-fill text-success'></i>"
                    : "<i class='bi bi-x-circle-fill text-danger'></i>")</td>
                <td>@Html.Raw(item.AllowWrite == true
                    ? "<i class='bi bi-check-circle-fill text-success'></i>"
                    : "<i class='bi bi-x-circle-fill text-danger'></i>")</td>
                <td>@Html.Raw(item.AllowModify == true
                    ? "<i class='bi bi-check-circle-fill text-success'></i>"
                    : "<i class='bi bi-x-circle-fill text-danger'></i>")</td>
                <td>@Html.Raw(item.AllowDelete == true
                    ? "<i class='bi bi-check-circle-fill text-success'></i>"
                    : "<i class='bi bi-x-circle-fill text-danger'></i>")</td>
                <td>@Html.Raw(item.AllowAll == true
                    ? "<i class='bi bi-check-circle-fill text-success'></i>"
                    : "<i class='bi bi-x-circle-fill text-danger'></i>")</td>
            </tr>
        }
    }
    else
    {
        <tr>
            <td colspan="8" class="text-center text-muted py-3">No data available</td>
        </tr>
    }
</tbody>

			
			
			
			
			int pageSize = 4;
            int skip = (page - 1) * pageSize;

            using (var connection = new SqlConnection(GetConnectionString()))
            {
                string sqlQuery = @"
select fp.UserID,emp.ema_ename,fd.Description,fp.AllowRead,fp.AllowWrite,fp.AllowModify,fp.AllowDelete,fp.AllowAll from App_UserFormPermission_NOPR fp
inner join App_FormDetails_NOPR fd 
on fp.FormId = fd.ID
inner join SAPHRDB.dbo.T_Empl_All emp
on fp.UserID = emp.ema_perno
WHERE
    (@search IS NULL OR fd.Id LIKE '%' + @search + '%')
 order by fp.UserID
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;
";

                string countQuery = @"
        SELECT COUNT(*) 
FROM App_UserFormPermission_NOPR k";

                var pagedData = await connection.QueryAsync<PermissionListDto>(sqlQuery, new
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
   
                var formList = context.AppFormDetails.Select(x => new AppFormDetail
                {
                    Id = x.Id,
                    Description = x.Description
                }).OrderBy(x => x.Description).ToList();
                ViewBag.formList = formList;


                var userPermissions = context.AppUserFormPermissions.ToList();
                ViewBag.UserPermissions = userPermissions;

                return View();
            }

 <form method="get" action="@Url.Action("UserPermission", "User")" class="d-flex">
<div class="col-md-2 mb-1" style="margin-left:10px;margin-right:10px;">
     <select class="form-control form-control-sm custom-select  me-2" name="searchString" style="height:80%;">
					<option value="">🔍 Form Name...</option>
					@foreach (var item in FormDropdown)
					{
						<option value="@item.Id" selected="@(item.Description == ViewBag.searchString ? "selected" : null)">@item.Description</option>
					}
					
					</select>


</div>

               
<button type="submit" class="btn btn-primary px-4 col-sm-2" style="width:12%;">Search</button>
     </form>

           <table class="table table-hover mb-0">
    <thead>
        <tr>
            <th>P.No.</th>
            <th>Name</th>
            <th>Form Name</th>
            <th>Read</th>
            <th>Write</th>
            <th>Modify</th>
            <th>Delete</th>
            <th>All</th>            
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
   data-id="@item.ID"">
   @item.UserId
</a>

</td>
                    <td>@item.ema_ename</td>
                    <td>@item.Description</td>
                    <td>@item.AllowRead</td>
                    <td>@item.AllowWrite</td>
                    <td>@item.AllowModify</td>
                    <td>@item.AllowDelete</td>
                    <td>@item.AllowAll</td>
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

      

        <div class="card-footer text-center">
            @if (ViewBag.TotalPages > 1)
            {
                <nav>
                    <ul class="pagination justify-content-center mb-0" style="font-size: 13px;">
                        <li class="page-item @(ViewBag.CurrentPage == 1 ? "disabled" : "")">
                            <a class="page-link" href="?page=@(ViewBag.CurrentPage - 1)&searchString=@ViewBag.searchString">Previous</a>
                        </li>

                        @for (int i = 1; i <= 5; i++)
                        {
                            <li class="page-item @(ViewBag.CurrentPage == i ? "active" : "")">
                                <a class="page-link" href="?page=@i&searchString=@ViewBag.searchString">@i</a>
                            </li>
                        }

                        <li class="page-item @(ViewBag.CurrentPage == ViewBag.TotalPages ? "disabled" : "")">
                            <a class="page-link" href="?page=@(ViewBag.CurrentPage + 1)&searchString=@ViewBag.searchString">Next</a>
                        </li>
                    </ul>
                </nav>
            }
        </div>
    </div>
