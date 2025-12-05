console:
Source IDs:f6851628-d446-4a7b-b569-31191c1d7d3a
DTRMaster:395 Source IDs:f6851628-d446-4a7b-b569-31191c1d7d3a,085fa7a4-a5cc-424c-ae63-b524a2225f47


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

            console.log("Source IDs:"+sourceIdsString);

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


 [HttpPost]
 public JsonResult GetFeedersBySource(List<string> sourceIds)
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
