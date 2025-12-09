warnings:

Interruption:492 The specified value "06-12-2025 00:00:00" does not conform to the required format.  The format is "yyyy-MM-ddThh:mm" followed by optional ":ss" or ":ss.SSS".

Interruption:493 The specified value "06-12-2025 00:00:00" does not conform to the required format.  The format is "yyyy-MM-ddThh:mm" followed by optional ":ss" or ":ss.SSS".
﻿


 refNoLinks.forEach(link => {
     link.addEventListener("click", function (event) {
         event.preventDefault();
         SourceMaster.style.display = "block";

         document.getElementById("DTRCapacity").value = this.getAttribute("data-DTRCapacity");
         document.getElementById("SourceID").value = this.getAttribute("data-SourceID");
          document.getElementById("NoOfConsumer").value = this.getAttribute("data-noofconsumer");
          document.getElementById("TypeOfInterruption").value = this.getAttribute("data-TypeOfInterruption");
          document.getElementById("FromDate").value = this.getAttribute("data-FromDate");
          document.getElementById("ToDate").value = this.getAttribute("data-ToDate");
           document.getElementById("InterruptionId").value = this.getAttribute("data-id");
         document.getElementById("DTR").value = this.getAttribute("data-DTRID");
        
        
         
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

FromDate	         ToDate
2025-12-06 12:14:00.000	 2025-12-07 12:14:00.000
