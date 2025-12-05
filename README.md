<div class="col-md-1">
    <label for="FeederName" class="control-label">Feeder</label>
</div>
<div class="col-md-3">
    <div class="dropdown">
        <input class="dropdown-toggle form-control form-control-sm custom-select text-start"
               type="button" id="FeederDropdown" data-bs-toggle="dropdown" aria-expanded="false" />

        <ul class="dropdown-menu w-100" aria-labelledby="FeederDropdown" id="FeederList">
            <li class="ms-2 text-muted">Select Source First</li>
        </ul>
    </div>

    <input type="hidden" id="FeederID" name="FeederID" />
</div>

[HttpPost]
public JsonResult GetFeeders(string sourceIds)
{
    string conn = GetConnection();

    string query = @"
        SELECT DISTINCT ID, FeederName
        FROM App_FeederMaster
        WHERE SourceID IN (SELECT value FROM STRING_SPLIT(@IDs, ','))";

    using (var con = new SqlConnection(conn))
    {
        var feeders = con.Query<AppFeederMaster>(query, new { IDs = sourceIds }).ToList();
        return Json(feeders);
    }
}

<script>
document.addEventListener("DOMContentLoaded", function () {

    const sourceCheckboxes = document.querySelectorAll(".Source-checkbox");
    const feederList = document.getElementById("FeederList");
    const feederDropdown = document.getElementById("FeederDropdown");
    const feederHidden = document.getElementById("FeederID");

    function loadFeeders() {

        // GET SELECTED SOURCE IDs
        let sourceIds = [];
        sourceCheckboxes.forEach(cb => {
            if (cb.checked) sourceIds.push(cb.value);
        });

        if (sourceIds.length === 0) {
            feederList.innerHTML = `<li class="ms-2 text-danger">Select Source First</li>`;
            feederDropdown.value = "Select Feeder";
            feederHidden.value = "";
            return;
        }

        // AJAX CALL
        fetch("/YourController/GetFeeders", {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({ sourceIds: sourceIds.join(",") })
        })
        .then(res => res.json())
        .then(data => {

            feederList.innerHTML = "";

            if (data.length === 0) {
                feederList.innerHTML = `<li class="ms-2 text-danger">No Feeder Found</li>`;
                return;
            }

            data.forEach(item => {
                feederList.innerHTML += `
                    <li style="margin-left:5%;">
                        <div class="form-check">
                            <input type="checkbox" class="form-check-input feeder-checkbox" 
                                   value="${item.id}" id="feed_${item.id}">
                            <label class="form-check-label" for="feed_${item.id}">
                                ${item.feederName}
                            </label>
                        </div>
                    </li>`;
            });

            attachFeederEvents();
        });
    }


    // Update Feeder Selected Names + Hidden Field
    function attachFeederEvents() {
        const feederCheckboxes = document.querySelectorAll(".feeder-checkbox");

        feederCheckboxes.forEach(cb => {
            cb.addEventListener("change", function () {

                let ids = [];
                let names = [];

                feederCheckboxes.forEach(f => {
                    if (f.checked) {
                        ids.push(f.value);
                        names.push(f.nextElementSibling.innerText.trim());
                    }
                });

                feederHidden.value = ids.join(",");
                feederDropdown.value = names.length ? names.join(", ") : "Select Feeder";
            });
        });
    }


    // WHEN ANY SOURCE IS CHANGED → RELOAD FEEDERS
    sourceCheckboxes.forEach(cb => {
        cb.addEventListener("change", loadFeeders);
    });

});
</script>


  
  
  <div class="col-md-1">
      <label for="SourceName" class="control-label">Source Name</label>
  </div>
  <div class="col-md-3">
      <div class="dropdown">
          <input class="dropdown-toggle form-control form-control-sm custom-select text-start" type="button"
                 id="SourceDropdown" data-bs-toggle="dropdown" aria-expanded="false" />
          <ul class="dropdown-menu w-100" aria-labelledby="SourceDropdown" id="SourceList">
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

          </ul>
      </div>
      <input type="hidden" id="SourceID" name="SourceID" />
  </div>

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

<script>
    document.addEventListener("DOMContentLoaded", function () {

        const checkboxes = document.querySelectorAll(".Source-checkbox");
        const hiddenInput = document.getElementById("SourceID");
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


select FeederName from App_FeederMaster where SourceID = 'f6851628-d446-4a7b-b569-31191c1d7d3a,085fa7a4-a5cc-424c-ae63-b524a2225f47'
