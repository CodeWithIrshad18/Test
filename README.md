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
