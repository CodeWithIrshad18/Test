 data-bs 
data-sourceid="f6851628-d446-4a7b-b569-31191c1d7d3a,085fa7a4-a5cc-424c-ae63-b524a2225f47" data-feederid="e43da0ea-9c88-4a1a-aafc-09bc234704cc,6f0e23ce-e251-46e2-a0fb-62bf13c1caa7"

Js : 
refNoLinks.forEach(link => {
    link.addEventListener("click", function (event) {
        event.preventDefault();
        SourceMaster.style.display = "block";

        document.getElementById("DTRCapacity").value = this.getAttribute("data-DTRCapacity");
        document.getElementById("DTRName").value = this.getAttribute("data-DTRName");
        document.getElementById("NoOfConsumer").value = this.getAttribute("data-NoOfConsumer");
        document.getElementById("DTRId").value = this.getAttribute("data-id");

        let existingSourceIds = this.getAttribute("data-sourceid");
        if (existingSourceIds) {
            loadExistingSources(existingSourceIds);
        }

        if (deleteButton) {
            deleteButton.style.display = "inline-block";
        }
    });
});

checkbox dropdown : 

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

i want based on the values when clicked on RefNoLink checked the values of checkbox
