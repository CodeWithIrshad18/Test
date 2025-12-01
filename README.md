Js:

<script>

    document.addEventListener("DOMContentLoaded", function () {
        var newButton = document.getElementById("newButton");
        var SourceMaster = document.getElementById("form");
        var refNoLinks = document.querySelectorAll(".refNoLink");
        var deleteButton = document.getElementById("deleteButton");
        var submitButton = document.getElementById("submitButton");
        var actionTypeInput = document.getElementById("actionType");

        if (newButton) {
            newButton.addEventListener("click", function () {
                SourceMaster.style.display = "block";
                document.getElementById("SourceID").value = "";     
                document.getElementById("FeederName").value = "";
                deleteButton.style.display = "none";
            });
        }

        refNoLinks.forEach(link => {
            link.addEventListener("click", function (event) {
                event.preventDefault();
                SourceMaster.style.display = "block";

                document.getElementById("FeederName").value = this.getAttribute("data-FeederName");
                document.getElementById("SourceID").value = this.getAttribute("data-SourceID");
                document.getElementById("FeederId").value = this.getAttribute("data-id");

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

checkbox dropdown :

                <div class="col-md-1">
        <label for="SourceName" class="control-label">Source Name</label>
    </div>
    <div class="col-md-3">
        <div class="dropdown">
            <input class="dropdown-toggle form-control form-control-sm custom-select text-start" type="button"
                   id="SourceDropdown" data-bs-toggle="dropdown" aria-expanded="false"/>
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
        <input type="hidden" id="SourceName" name="SourceID" />
    </div>

anchor tag which has sourceID values 

<a href="#" class="refNoLink" data-id="e43da0ea-9c88-4a1a-aafc-09bc234704cc" data-sourceid="f6851628-d446-4a7b-b569-31191c1d7d3a,085fa7a4-a5cc-424c-ae63-b524a2225f47" data-feedername="testing for feedeer" data-createdby="151514">
    testing for feedeer
</a>

i want to checked the value of source from for existing data when clicked on refNoLink
