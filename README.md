this is my dropdown 

var WorksiteList = context.AppLocationMasters
    .Select(x => new SelectListItem
    {
        Value = x.Id.ToString(),
        Text = x.WorkSite
    }).Distinct().OrderBy(x => x.Text).ToList();


ViewBag.WorksiteDDList = WorksiteList;

 <select  asp-items="@ViewBag.WorksiteDDList" class="form-control custom-select" name="WorksiteName">
<option value="">select Worksite</option>
</select>
 </div>
