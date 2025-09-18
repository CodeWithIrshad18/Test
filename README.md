<select asp-items="@ViewBag.WorksiteDDList" 
        class="form-control custom-select" 
        name="WorksiteName">
    <option value="">select Worksite</option>
</select>

public IActionResult Index(string WorksiteName)
{
    var WorksiteList = context.AppLocationMasters
        .Select(x => new SelectListItem
        {
            Value = x.Id.ToString(),
            Text = x.WorkSite
        })
        .Distinct()
        .OrderBy(x => x.Text)
        .ToList();

    // set selected value back
    ViewBag.WorksiteDDList = new SelectList(WorksiteList, "Value", "Text", WorksiteName);

    var query = context.App_Position_Worksite.AsQueryable();

    if (!string.IsNullOrEmpty(WorksiteName))
    {
        query = query.Where(p => p.Worksite.Contains(WorksiteName));
    }

    return View(query.ToList());
}




this is my dropdown 

var WorksiteList = context.AppLocationMasters
    .Select(x => new SelectListItem
    {
        Value = x.Id.ToString(),
        Text = x.WorkSite
    }).Distinct().OrderBy(x => x.Text).ToList();


ViewBag.WorksiteDDList = WorksiteList;

 <select  asp-items="@ViewBag.WorksiteDDList" class="form-control custom-select"
     
     
     name="WorksiteName">
<option value="">select Worksite</option>
</select>
 </div>
