[HttpPost]
public JsonResult GetFeedersBySource([FromBody] string[] sourceIds)
{
    string conn = GetConnection();

    using (var connection = new SqlConnection(conn))
    {
        // Build OR conditions
        string whereClause = string.Join(" OR ", 
            sourceIds.Select((id, index) => $"SourceID LIKE @s{index}")
        );

        string query = $@"
            SELECT DISTINCT ID, FeederName 
            FROM App_FeederMaster
            WHERE {whereClause}
        ";

        DynamicParameters parameters = new DynamicParameters();

        for (int i = 0; i < sourceIds.Length; i++)
        {
            parameters.Add($"s{i}", "%" + sourceIds[i] + "%");
        }

        var list = connection.Query<AppFeederMaster>(query, parameters).ToList();
        return Json(list);
    }
}



<div class="form-check">
                            <input type="checkbox" class="form-check-input Feeder-checkbox" value="undefined" id="f_undefined">
                            <label class="form-check-label" for="f_undefined">undefined</label>


</div>


 [HttpPost]
 public JsonResult GetFeedersBySource([FromBody] string[] sourceIds)
 {
     string conn = GetConnection();

     string query = @"SELECT DISTINCT ID, FeederName 
              FROM App_FeederMaster 
              WHERE SourceID IN @sourceIds";

     using (var connection = new SqlConnection(conn))
     {
         var list = connection.Query<AppFeederMaster>(query, new { sourceIds }).ToList();
         return Json(list);
     }
 }


  <div class="col-md-1">
      <label for="FeederName" class="control-label">Feeder Name</label>
  </div>

  <div class="col-md-3">
      <div class="dropdown">
          <input class="dropdown-toggle form-control form-control-sm custom-select text-start"
                 type="button" id="FeederDropdown" data-bs-toggle="dropdown" aria-expanded="false"
                 value="Select Feeder" />

          <ul class="dropdown-menu w-100" aria-labelledby="FeederDropdown" id="FeederList">
              <!-- Items will be injected via JS -->
          </ul>
      </div>

      <input type="hidden" id="FeederID" name="FeederID" />
  </div>



<script>
    document.addEventListener("DOMContentLoaded", function () {

        const sourceCheckboxes = document.querySelectorAll(".Source-checkbox");
        const sourceHidden = document.getElementById("SourceID");
        const sourceDropdownBtn = document.getElementById("SourceDropdown");

        function updateSources() {
            let ids = [];
            let names = [];

            sourceCheckboxes.forEach(cb => {
                if (cb.checked) {
                    ids.push(cb.value);
                    names.push(cb.nextElementSibling.innerText.trim());
                }
            });

            sourceHidden.value = ids.join(",");
            sourceDropdownBtn.value = names.length ? names.join(", ") : "Select Source";

            loadFeeders();
        }

        sourceCheckboxes.forEach(cb => cb.addEventListener("change", updateSources));

        // ---------------- FEEDER DROPDOWN ----------------
        function loadFeeders() {

            let sourceIdsString = document.getElementById("SourceID").value;

            // console.log("Source IDs:"+sourceIdsString);

            if (!sourceIdsString) {
                document.getElementById("FeederList").innerHTML =
                    "<li class='ms-2'>Select source first</li>";

                document.getElementById("FeederDropdown").value = "Select Feeder";
                document.getElementById("FeederID").value = "";

                return;
            }

         
            let sourceIds = sourceIdsString.split(",");

            fetch('/Master/GetFeedersBySource', {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify(sourceIds)  // <-- Always works
            })
            .then(response => response.json())
            .then(data => {
                renderFeederList(data);
            });
        }

        function renderFeederList(data) {
            let feederList = document.getElementById("FeederList");
            feederList.innerHTML = "";

            if (data.length === 0) {
                feederList.innerHTML = "<li class='ms-2 text-danger'>No Feeder Found</li>";
                return;
            }

            data.forEach(item => {
                feederList.innerHTML += `
                    <li style="margin-left:5%;">
                        <div class="form-check">
                            <input type="checkbox" class="form-check-input Feeder-checkbox"
                                   value="${item.ID}" id="f_${item.ID}" />
                            <label class="form-check-label" for="f_${item.ID}">${item.FeederName}</label>
                        </div>
                    </li>`;
            });

            attachFeederEvents();
        }

        function attachFeederEvents() {
            const feederCheckboxes = document.querySelectorAll(".Feeder-checkbox");
            const feederHidden = document.getElementById("FeederID");
            const feederDropdownBtn = document.getElementById("FeederDropdown");

            function updateFeeders() {
                let ids = [];
                let names = [];

                feederCheckboxes.forEach(cb => {
                    if (cb.checked) {
                        ids.push(cb.value);
                        names.push(cb.nextElementSibling.innerText.trim());
                    }
                });

                feederHidden.value = ids.join(",");
                feederDropdownBtn.value = names.length ? names.join(", ") : "Select Feeder";
            }

            feederCheckboxes.forEach(cb => cb.addEventListener("change", updateFeeders));
        }

    });
</script>

table data : 
ID	FeederName	SourceID	CreatedBy	CreatedOn
E43DA0EA-9C88-4A1A-AAFC-09BC234704CC	testing for feedeer	f6851628-d446-4a7b-b569-31191c1d7d3a,085fa7a4-a5cc-424c-ae63-b524a2225f47	151514	2025-12-01 10:12:17.720
6F0E23CE-E251-46E2-A0FB-62BF13C1CAA7	test feeder	085fa7a4-a5cc-424c-ae63-b524a2225f47	151514	2025-12-05 12:10:56.807
