 <a href="#"
    class="refNoLink"
    data-id="@item.ID"
    data-DTRName="@item.DTRName"
    data-SourceName="@item.SourceName"
    data-SourceID="@item.SourceID"
    data-FeederID="@item.FeederID"
    data-DTRID="@item.DTRID"
    data-TypeOfInterruption="@item.TypeOfInterruption"
    data-FeederName="@item.FeederName"
    data-FromDate="@item.FromDate"
    data-ToDate="@item.ToDate"
    data-DTRCapacity="@item.DTRCapacity"
    data-NoOfConsumer="@item.NoOfConsumer">
     @item.DTRName
 </a>


 <div class="col-md-1">
     <label for="FeederID" class="control-label">Feeder</label>
 </div>
 <div class="col-md-2">
     <select asp-for="FeederID" id="FeederID" class="form-control form-control-sm custom-select">
         <option value=""></option>
     </select>
 </div>

 <div class="col-md-1">
     <label for="FeederID" class="control-label">DTR </label>
 </div>
 <div class="col-md-2">
     <select asp-for="DTRID" id="DTR" class="form-control form-control-sm custom-select">
         <option value=""></option>
     </select>
 </div>

js for existing data  

       refNoLinks.forEach(link => {
    link.addEventListener("click", function (event) {
        event.preventDefault();
        SourceMaster.style.display = "block";

        document.getElementById("DTRCapacity").value = this.getAttribute("data-DTRCapacity");
        document.getElementById("SourceID").value = this.getAttribute("data-SourceID");
        document.getElementById("NoOfConsumer").value = this.getAttribute("data-noofconsumer");
        document.getElementById("TypeOfInterruption").value = this.getAttribute("data-TypeOfInterruption");

        document.getElementById("FromDate").value =
            formatDateForInput(this.getAttribute("data-FromDate"));

        document.getElementById("ToDate").value =
            formatDateForInput(this.getAttribute("data-ToDate"));

        document.getElementById("InterruptionId").value = this.getAttribute("data-id");
        document.getElementById("DTR").value = this.getAttribute("data-DTRID");

        if (deleteButton) {
            deleteButton.style.display = "inline-block";
        }
    });

this when changes on dropdown of source and feeder 

<script>
    $(document).ready(function () {

        $("#SourceID").change(function () {
            let sourceId = $(this).val();
            $("#FeederID").html('<option value="">Select Feeder</option>');
            $("#DTR").html('<option value="">Select DTR</option>');
            $("#DTRCapacity").val('');
            $("#NoOfConsumer").val('');

            if (sourceId) {
                $.get("/PS/GetFeeders", { sourceId: sourceId }, function (data) {
                    $.each(data, function (i, item) {
                        $("#FeederID").append('<option value="' + item.id + '">' + item.feederName + '</option>');
                    });
                });
            }
        });

        $("#FeederID").change(function () {
            let feederId = $(this).val();
            let sourceId = $("#SourceID").val();

            $("#DTR").html('<option value="">Select DTR</option>');
            $("#DTRCapacity").val('');
            $("#NoOfConsumer").val('');

            if (feederId && sourceId) {
                $.get("/PS/GetDTR",
                    { sourceId: sourceId, feederId: feederId },
                    function (data) {
                        $.each(data, function (i, item) {
                            $("#DTR").append('<option value="' + item.id + '">' + item.dtrName + '</option>');
                        });
                    }
                );
            }
        });

            $("#DTR").change(function () {

        let dtrName = $("#DTR option:selected").text();  
        let sourceId = $("#SourceID").val();
        let feederId = $("#FeederID").val();

        if (sourceId && feederId && dtrName) {
            $.get("/PS/GetDTRDetails",
                {
                    sourceId: sourceId,
                    feederId: feederId,
                    dtrName: dtrName
                },
                function (data) {
                    $("#DTRCapacity").val(data.DTRCapacity);
                    $("#NoOfConsumer").val(data.NoOfConsumer);
                }
            );
        }
    });

    });
</script>
});
