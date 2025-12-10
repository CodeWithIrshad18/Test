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
                    "To Date must be greater than From Date";
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


<script>


        function loadExistingDropdownValues(sourceId, feederId, dtrId) {


        $("#SourceID").val(sourceId).trigger("change");

  
        $.get("/PS/GetFeeders", { sourceId: sourceId }, function (feeders) {

            $("#FeederID").html('<option value="">Select Feeder</option>');
            $.each(feeders, function (i, item) {
                $("#FeederID").append('<option value="' + item.id + '">' + item.feederName + '</option>');
            });

            $("#FeederID").val(feederId).trigger("change");

     
            $.get("/PS/GetDTR",
                { sourceId: sourceId, feederId: feederId },
                function (dtrs) {

                    $("#DTR").html('<option value="">Select DTR</option>');
                    $.each(dtrs, function (i, item) {
                        $("#DTR").append('<option value="' + item.id + '">' + item.dtrName + '</option>');
                    });

                    $("#DTR").val(dtrId).trigger("change");
                }
            );
        });
    }


    document.addEventListener("DOMContentLoaded", function () {

        var newButton = document.getElementById("newButton");
        var SourceMaster = document.getElementById("form");
        var refNoLinks = document.querySelectorAll(".refNoLink");
        var deleteButton = document.getElementById("deleteButton");
        var submitButton = document.getElementById("submitButton");
        var actionTypeInput = document.getElementById("actionType");

        const checkboxes = document.querySelectorAll(".Source-checkbox");
        const hiddenInput = document.getElementById("SourceID");
        const dropdownBtn = document.getElementById("SourceDropdown");


            if (newButton) {
        newButton.addEventListener("click", function () {
            SourceMaster.style.display = "block";

          
            $("#SourceID").val("").trigger("change");

     
            $("#FeederID").html('<option value="">Select Feeder</option>');
            $("#FeederID").val("");

           
            $("#DTR").html('<option value="">Select DTR</option>');
            $("#DTR").val("");

          
            $("#FromDate").val("");
            $("#ToDate").val("");
            $("#DTRCapacity").val("");
            $("#NoOfConsumer").val("");
            $("#TypeOfInterruption").val("");
            $("#InterruptionId").val("");
            $("#DurationLabel").val("");
            hiddenInput.value = "";

         
            checkboxes.forEach(cb => cb.checked = false);

          
            if (deleteButton) {
                deleteButton.style.display = "none";
            }
        });
    }

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


        submitButton.addEventListener("click", function () {
            actionTypeInput.value = "save";
        });


        if (deleteButton) {
            deleteButton.addEventListener("click", function () {
                Swal.fire({
                    title: 'Are you sure?',
                    text: "Do you really want to delete this Source?",
                    icon: 'warning',
                    showCancelButton: true,
                    confirmButtonColor: '#3085d6',
                    cancelButtonColor: '#d33',
                    confirmButtonText: 'Yes, delete it!',
                    cancelButtonText: 'Cancel'
                }).then((result) => {
                    if (result.isConfirmed) {
                        actionTypeInput.value = "delete";
                        document.getElementById("form").submit();
                    }
                });
            });
        }
        

    });

</script>
