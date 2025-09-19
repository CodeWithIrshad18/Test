<div class="col-md-3">
				<input type="text" name="PositionId" class="form-control" value="@ViewBag.PositionId" placeholder="Position ID." autocomplete="off" />
</div>


<div class="col-md-3">
				 <select  asp-items="@ViewBag.WorksiteDDList" class="form-control custom-select" name="WorksiteName">
							<option value="">select Worksite</option>
						</select>
            </div>



if (!string.IsNullOrEmpty(searchString))
{
    empList = empList
        .Where(e => ((string)e.Pno).Contains(searchString, StringComparison.OrdinalIgnoreCase)
                 || (!string.IsNullOrEmpty((string)e.Name) && ((string)e.Name).Contains(searchString, StringComparison.OrdinalIgnoreCase)))
        .ToList();
}

I want to filter using these two textbox and dropdown 
