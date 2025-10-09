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
