<div class="col-md-12 mt-2">
    <label id="DurationLabel" class="fw-bold text-primary"></label>
</div>

  <script>
    function calculateDuration() {
        let from = document.getElementById("FromDate").value;
        let to = document.getElementById("ToDate").value;

        if (from && to) {
            let fromDate = new Date(from);
            let toDate = new Date(to);

            let diffMs = toDate - fromDate;

            if (diffMs < 0) {
                document.getElementById("DurationLabel").innerText =
                    "⚠️ To Date must be greater than From Date";
                return;
            }

            let diffMinutes = Math.floor(diffMs / 60000);
            let days = Math.floor(diffMinutes / (60 * 24));
            let hours = Math.floor((diffMinutes % (60 * 24)) / 60);
            let minutes = diffMinutes % 60;

            document.getElementById("DurationLabel").innerText =
                `Duration: ${days} Days ${hours} Hours ${minutes} Minutes`;
        }
    }

    document.getElementById("FromDate").addEventListener("change", calculateDuration);
    document.getElementById("ToDate").addEventListener("change", calculateDuration);
</script>
            
              
              
              refNoLinks.forEach(link => {
        link.addEventListener("click", function (event) {
            event.preventDefault();
            SourceMaster.style.display = "block";

            let sourceId = this.getAttribute("data-SourceID");
            let feederId = this.getAttribute("data-FeederID");
            let dtrId = this.getAttribute("data-DTRID");

   
            loadExistingDropdownValues(sourceId, feederId, dtrId);

            // Existing fields
            document.getElementById("DTRCapacity").value = this.getAttribute("data-DTRCapacity");
            document.getElementById("NoOfConsumer").value = this.getAttribute("data-NoOfConsumer");
            document.getElementById("TypeOfInterruption").value = this.getAttribute("data-TypeOfInterruption");

            document.getElementById("FromDate").value = formatDateForInput(this.getAttribute("data-FromDate"));
            document.getElementById("ToDate").value = formatDateForInput(this.getAttribute("data-ToDate"));
            document.getElementById("InterruptionId").value = this.getAttribute("data-id");

            if (deleteButton) {
                deleteButton.style.display = "inline-block";
            }
        });
    });
 
 
 
 <div class="col-md-1">
     <label for="FromDate" class="control-label">From Date</label>
 </div>
 <div class="col-md-2">
     <input asp-for="FromDate" type="datetime-local" class="form-control form-control-sm" id="FromDate" name="FromDate">
 </div>
 <div class="col-md-1">
     <label for="ToDate" class="control-label">To Date</label>
 </div>
 <div class="col-md-2">
     <input asp-for="ToDate" type="datetime-local" class="form-control form-control-sm" id="ToDate" name="ToDate">
 </div>


please add a lable below this and count the duration of fromDate and ToDate
