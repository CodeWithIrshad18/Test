<script>
document.addEventListener("DOMContentLoaded", function () {

    const checkboxes = document.querySelectorAll(".Source-checkbox");
    const hiddenInput = document.getElementById("SourceName");
    const dropdownBtn = document.getElementById("SourceDropdown");

    function updateSelectedSources() {
        let selectedIds = [];
        let selectedNames = [];

        checkboxes.forEach(cb => {
            if (cb.checked) {
                selectedIds.push(cb.value);
                selectedNames.push(cb.nextElementSibling.innerText.trim());
            }
        });

        // Store selected IDs in hidden field (for form submit)
        hiddenInput.value = selectedIds.join(",");

        // Show selected names in dropdown button
        dropdownBtn.value = selectedNames.length > 0
            ? selectedNames.join(", ")
            : "Select Source";
    }

    checkboxes.forEach(cb => {
        cb.addEventListener("change", updateSelectedSources);
    });

});
</script>


@{
    var sources = ViewBag.SourceDropdown as List<AppSourceMaster>;
}

@if (sources != null && sources.Any())
{
    foreach (var item in sources)
    {
        <li style="margin-left:5%;">
            <div class="form-check">
                <input type="checkbox" class="form-check-input Source-checkbox"
                       value="@item.ID" id="div_@item.ID" />
                <label class="form-check-label" for="div_@item.ID">
                    @item.SourceName
                </label>
            </div>
        </li>
    }
}
else
{
    <li class="text-danger ms-2">No Source Found</li>
}

ViewBag.SourceDropdown = GetSourceDD(); ✅ correct


 public List<AppSourceMaster> GetSourceDD()
 {
     string connectionString = GetConnection();

     string query = @"select distinct ID,SourceName from App_SourceMaster where SourceName is not null";


     using (var connection = new SqlConnection(connectionString))
     {
         var Source = connection.Query<AppSourceMaster>(query).ToList();

         return Source;

     }

 }


            <div class="col-md-2">
    <label for="SourceName" class="control-label">Source Name</label>
</div>
<div class="col-md-2">
    <div class="dropdown">
        <input class="dropdown-toggle form-control form-control-sm custom-select text-start" type="button"
               id="SourceDropdown" data-bs-toggle="dropdown" aria-expanded="false"/>
        <ul class="dropdown-menu w-100" aria-labelledby="SourceDropdown" id="SourceList">
            @foreach (var item in ViewBag.SourceDropdown as List<AppSourceMaster>)
            {
                <li style="margin-left:5%;">
                    <div class="form-check">
                        <input type="checkbox" class="form-check-input Source-checkbox"
                               value="@item.ID" id="div_@item.SourceName" />
                        <label class="form-check-label" for="div_@item.SourceName">
                            @item.SourceName
                        </label>
                    </div>
                </li>
            }
        </ul>
    </div>
    <input type="hidden" id="SourceName" name="SourceName" />
</div>


 ViewBag.source = GetSourceDD();
