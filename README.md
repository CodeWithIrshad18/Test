this is my script 

<script>

    document.addEventListener("DOMContentLoaded", function () {
        var newButton = document.getElementById("newButton");
        var UOMMaster = document.getElementById("form");
        var refNoLinks = document.querySelectorAll(".refNoLink");
        var deleteButton = document.getElementById("deleteButton");
        var submitButton = document.getElementById("submitButton");
        var actionTypeInput = document.getElementById("actionType");

        if (newButton) {
            newButton.addEventListener("click", function () {
                UOMMaster.style.display = "block";
                document.getElementById("UnitCode").value = "";
                document.getElementById("UnitDescription").value = "";
                document.getElementById("Comparision").value = "";            
                document.getElementById("UOMId").value = "";
                deleteButton.style.display = "none";
            });
        }

        refNoLinks.forEach(link => {
            link.addEventListener("click", function (event) {
                event.preventDefault();
                UOMMaster.style.display = "block";

                document.getElementById("UnitCode").value = this.getAttribute("data-UnitCode");
                document.getElementById("UnitDescription").value = this.getAttribute("data-UnitDescription");
                document.getElementById("Comparision").value = this.getAttribute("data-Comparision");
                document.getElementById("UOMId").value = this.getAttribute("data-id");

                if (deleteButton) {
                    deleteButton.style.display = "inline-block";
                }
            });
        });

        submitButton.addEventListener("click", function () {
            actionTypeInput.value = "save";  // Set action type to "save"
        });

        if (deleteButton) {
            deleteButton.addEventListener("click", function () {
                if (confirm("Are you sure you want to delete this Subject?")) {
                    actionTypeInput.value = "delete";  // Set action type to "delete"
                    document.getElementById("form").submit();  // Submit form
                }
            });
        }
    });




</script>

when I click on this the form is not opening with the data 
  <a asp-action="UnitOfMeasurement" asp-route-id="@item.ID"
    style="text-decoration:none;font-weight:600;color:#5a3ec8;"
    data-id="@item.ID"
    data-UnitCode="@item.UnitCode"
    data-createdBy="@item.CreatedBy"
    data-UnitDescription="@item.UnitDescription"
    data-Comparision="@item.Comparision">
     @item.UnitCode
 </a> 
