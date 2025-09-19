public async Task<IActionResult> PositionMaster(Guid? id, AppPositionWorksite appPosition, int page = 1, string searchValue = "", string PositionId = "", string WorksiteName = "")
{
    var UserId = HttpContext.Request.Cookies["Session"];

    var empList = await context.AppEmployeeMasters.ToListAsync();

    // search text (Pno or Name)
    if (!string.IsNullOrEmpty(searchValue))
    {
        empList = empList
            .Where(e =>
                ((string)e.Pno).Contains(searchValue, StringComparison.OrdinalIgnoreCase) ||
                (!string.IsNullOrEmpty((string)e.Name) && ((string)e.Name).Contains(searchValue, StringComparison.OrdinalIgnoreCase))
            )
            .ToList();
    }

    // filter by PositionId (textbox)
    if (!string.IsNullOrEmpty(PositionId))
    {
        empList = empList
            .Where(e => !string.IsNullOrEmpty(e.PositionId) && e.PositionId.Contains(PositionId, StringComparison.OrdinalIgnoreCase))
            .ToList();
    }

    // filter by WorksiteName (dropdown)
    if (!string.IsNullOrEmpty(WorksiteName))
    {
        empList = empList
            .Where(e => e.WorksiteName == WorksiteName)
            .ToList();
    }

    return View(empList);
}



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
