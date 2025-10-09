<style>
    /* General look */
    body {
        font-family: 'Segoe UI', sans-serif;
        background-color: #f8f9fb;
    }

    .card-custom {
        border-radius: 10px;
        box-shadow: 0 3px 10px rgba(0, 0, 0, 0.1);
        background-color: #fff;
        border: none;
    }

    .card-header-custom {
        background: linear-gradient(90deg, #6f42c1, #b084f3);
        color: #fff;
        font-weight: 600;
        padding: 10px 15px;
        border-radius: 10px 10px 0 0;
        font-size: 16px;
    }

    .btn-primary {
        background-color: #6f42c1;
        border: none;
    }

    .btn-primary:hover {
        background-color: #59359c;
        box-shadow: 0 0 5px rgba(111, 66, 193, 0.5);
    }

    .btn-danger:hover {
        box-shadow: 0 0 5px rgba(220, 53, 69, 0.4);
    }

    table {
        font-size: 14px;
    }

    .table thead th {
        background-color: #ede3ff;
        color: #3b2d60;
        font-weight: 600;
    }

    .table tbody tr:hover {
        background-color: #f4f0ff;
    }

    .search-bar input {
        border-radius: 6px;
        border: 1px solid #c7c7c7;
    }

    .pagination .page-link {
        color: #6f42c1;
    }

    .pagination .active .page-link {
        background-color: #6f42c1;
        border-color: #6f42c1;
        color: #fff;
    }

    label {
        font-size: 14px;
        font-weight: 500;
    }

    .form-control-sm {
        border-radius: 6px;
    }

    #form {
        margin-top: 10px;
    }
</style>

<div class="container mt-3">

    <!-- Search Section -->
    <div class="card card-custom mb-3">
        <div class="card-body">
            <div class="row align-items-center">
                <div class="col-md-8 d-flex">
                    <input type="text" name="UOM" class="form-control me-2" value="@ViewBag.UOM" placeholder="🔍 Search by Unit..." autocomplete="off" />
                    <button type="submit" class="btn btn-primary px-4">Search</button>
                </div>
                <div class="col-md-4 text-end mt-2 mt-md-0">
                    <button type="button" class="btn btn-primary" id="newButton">
                        <i class="bi bi-plus-circle me-1"></i> New
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- Data Table -->
    <div class="card card-custom">
        <div class="card-header-custom">Unit of Measurement List</div>
        <div class="card-body p-0">
            <table class="table table-hover mb-0">
                <thead>
                    <tr>
                        <th width="25%">Unit of Measurement</th>
                        <th>Description</th>
                        <th width="20%">Logic for Reporting</th>
                    </tr>
                </thead>
                <tbody>
                    @if (ViewBag.ListData2 != null)
                    {
                        @foreach (var item in ViewBag.ListData2)
                        {
                            <tr>
                                <td>
                                    <a asp-action="UnitOfMesurement" asp-route-id="@item.ID"
                                       style="text-decoration:none;font-weight:600;color:#5a3ec8;"
                                       data-id="@item.ID"
                                       data-UnitCode="@item.UnitCode"
                                       data-createdBy="@item.CreatedBy"
                                       data-UnitDescription="@item.UnitDescription"
                                       data-Comparision="@item.Comparision">
                                        @item.UnitCode
                                    </a>
                                </td>
                                <td>@item.UnitDescription</td>
                                <td>@item.Comparision</td>
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

        <!-- Pagination -->
        <div class="card-footer text-center">
            @if (ViewBag.TotalPages > 1)
            {
                <nav>
                    <ul class="pagination justify-content-center mb-0">
                        <li class="page-item @(ViewBag.CurrentPage == 1 ? "disabled" : "")">
                            <a class="page-link" href="?page=@(ViewBag.CurrentPage - 1)&searchString=@ViewBag.UOM">Previous</a>
                        </li>

                        @for (int i = 1; i <= ViewBag.TotalPages; i++)
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
    </div>

    <!-- Form Section -->
    <form asp-action="UnitOfMeasurement" asp-controller="Master" id="form" method="post" style="display:none;">
        <div class="card card-custom mt-4">
            <div class="card-header-custom">Unit of Measurement Master</div>
            <div class="card-body">
                <div class="row g-3">
                    <input type="hidden" asp-for="ID" id="UOMId" />
                    <input type="hidden" id="actionType" name="actionType" />

                    <div class="col-md-4">
                        <label for="UnitCode">Unit of Measurement</label>
                        <input asp-for="UnitCode" class="form-control form-control-sm" id="UnitCode" autocomplete="off">
                    </div>

                    <div class="col-md-4">
                        <label for="UnitDescription">Description</label>
                        <input asp-for="UnitDescription" class="form-control form-control-sm" id="UnitDescription" autocomplete="off">
                    </div>

                    <div class="col-md-4">
                        <label for="Comparision">Logic for Reporting</label>
                        <select asp-for="Comparision" class="form-control form-control-sm" id="Comparision">
                            <option></option>
                            <option value="Direct">Direct</option>
                            <option value="Converted">Converted</option>
                        </select>
                    </div>
                </div>

                <div class="text-center mt-4">
                    @if (ViewBag.CanModify == true || ViewBag.CanWrite == true)
                    {
                        <button class="btn btn-primary me-2 px-4" id="submitButton" type="submit">Submit</button>
                    }
                    @if (ViewBag.CanDelete == true)
                    {
                        <button class="btn btn-danger px-4" id="deleteButton" style="display:none;">Delete</button>
                    }
                </div>
            </div>
        </div>
    </form>
</div>



I have this design for my form 

<style>
  

        .btn:hover,
        .btn:focus {
            color: #4E4C97;
            background-color: #fff;
            box-shadow: 0 0 5px #4E4C97, 0 0 15px #4E4C97 inset;
        }

    .table > tbody > tr > td {
        border: 1px solid #939393;
        padding: 6px;
        padding-left: 10px;
    }

    .btn {

    font-size:14px;
}
</style>

<fieldset style="border:1px solid #bfbebe;padding: 4px 5px 0px 5px;border-radius:6px;" class="fieldset">
<div class="row align-items-center">
    <div class="col-md-9">
        <!-- Search Form -->
        <form method="get" action="@Url.Action("UnitOfMeasurement")" style="display:flex;">

            <div class="col-md-4">

                    <input type="text" name="UOM" class="form-control" value="@ViewBag.UOM" placeholder="Search by Unit ..." autocomplete="off" />
            </div>

            <div class="col-md-3" style="padding-left:1%;">

                <button type="submit" class="btn btn-primary">Search</button>
            </div>



        </form>
    </div>
    <div class="col-md-3 mb-2 text-end">
        <button type="submit" class="btn btn-primary" id="newButton">New</button>

    </div>

</div>
    <table class="table" id="myTable">
        <thead class="table" style="background-color: #d2b1ff;color: #000000;font-size:14px;">
            <tr>

                <th width="25%">Unit of Measurement</th>
                <th>Description</th>
                <th  width="20%">Logic for Reporting</th>

            </tr>
        </thead>
        <tbody style="">
            @if (ViewBag.ListData2 != null)
            {
                @foreach (var item in ViewBag.ListData2)
                {
                    <tr>
                        <td>
                            <a asp-action="UnitOfMesurement" asp-route-id="@item.ID" class="control-label refNoLink" style="text-decoration: none;background-color: #ffffff;font-weight: bolder;color: darkblue;"
                               data-id="@item.ID" data-UnitCode="@item.UnitCode" data-createdBy="@item.CreatedBy" data-UnitDescription="@item.UnitDescription" data-Comparision="@item.Comparision">
                                @item.UnitCode
                            </a>
                        </td>

                       
                       
                        <td class="control-label">@item.UnitDescription</td>
                        <td class="control-label">@item.Comparision</td>
                    </tr>
                }
            }
            else
            {
                <tr>
                    <td colspan="4">No data available</td>
                </tr>
            }
        </tbody>
    </table>
    <div class="text-center">
        @if (ViewBag.TotalPages > 1)
        {
            <nav>
                <ul class="pagination justify-content-center" style="font-size:12px;font-family:Arial;">
                    <!-- Previous Button -->
                    <li class="page-item @(ViewBag.CurrentPage == 1 ? "disabled" : "")">
                        <a class="page-link" href="?page=@(ViewBag.CurrentPage - 1)&searchString=@ViewBag.UOM" tabindex="-1">Previous</a>
                    </li>


                    <!-- Page Numbers -->
                    <li class="page-item @(ViewBag.CurrentPage == 1 ? "active" : "")">
                        <a class="page-link" href="?page=1&searchString=@ViewBag.UOM">1</a>
                    </li>
                    @if (ViewBag.TotalPages > 1)
                    {
                        <li class="page-item @(ViewBag.CurrentPage == 2 ? "active" : "")">
                            <a class="page-link" href="?page=2&searchString=@ViewBag.UOM">2</a>
                        </li>
                    }

                    <!-- Next Button -->
                    <li class="page-item @(ViewBag.CurrentPage == ViewBag.TotalPages ? "disabled" : "")">
                        <a class="page-link" href="?page=@(ViewBag.CurrentPage + 1)&searchString=@ViewBag.UOM">Next</a>
                    </li>
                </ul>
            </nav>
        }
    </div>
    


</fieldset>

    <div class="card-header text-center mt-2" style="background-color:rgb(210 177 255);font-weight: 800;padding: 0%;font-size: 15px;background-color: rgb(210 177 255);font-weight: 800;margin: 7px 9px -8px 9px;">Unit of Measurement Master</div>
    <div class="col-md-12 p-2">

        
        <form asp-action="UnitOfMeasurement" id="form" asp-controller="Master" method="post" style="display:none;">

            <div class="card rounded-9">
        <fieldset style="border:1px solid #bfbebe;padding:20px 20px 5px 20px;border-radius:6px;">
                <input type="hidden" asp-for="ID" id="UOMId" name="ID" />
                <input type="hidden" id="actionType" name="actionType" />  <!-- Hidden field for action type -->

                <div class="row">
                    <div class="col-sm-1">
                        <label for="UnitCode" class="control-label">Unit of Measurement</label>
                    </div>

                    <div class="col-sm-3">
                        <input asp-for="UnitCode" class="form-control form-control-sm" id="UnitCode" type="text" autocomplete="off">
                    </div>
                    <div class="col-sm-1">
                        <label for="UnitDescription" class="control-label">Description</label>
                    </div>

                    <div class="col-sm-3">
                        <input asp-for="UnitDescription" class="form-control form-control-sm" id="UnitDescription"  type="text" autocomplete="off">
                    </div>
                    <div class="col-sm-1">
                        <label for="Comparision" class="control-label">Logic for Reporting</label>
                    </div>

                    <div class="col-sm-3">
                       <Select asp-for="Comparision" class="form-control form-control-sm custom-select" id="Comparision"  type="text">
    <option></option>
    <option value="Direct">Direct</option>
    <option value="Converted">Converted</option>
</Select>
                    </div>
                </div>

                <div class="mt-4 text-center">
                @if (ViewBag.CanModify == true || ViewBag.CanWrite == true)
						{
                    <button class="btn btn-primary" id="submitButton" type="submit">Submit</button>
                        }
                        @if (ViewBag.CanDelete == true)
						{
                    <button class="btn btn-danger" id="deleteButton" style="display: none;">Delete</button>
                        }
                </div>
            </form>


    </fieldset>
</div>
</div>

but I don't satisfy , it is not looking good , see the reference image and provide me clean and modern ui of form to look good
