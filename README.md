    <form asp-action="PeriodicityMaster" asp-controller="Master" id="form" method="post" style="display:none;">
        <div class="card card-custom mt-4">
            <div class="card-header-custom">Periodicity Master</div>
            <div class="card-body">
                <div class="row g-3">
                    <input type="hidden" asp-for="ID" id="PeriodicityId" name="ID" />
                    <input type="hidden" id="actionType" name="actionType" />

                    <div class="col-md-4">
                        <label for="PeriodicityCode" class="control-label">Periodicity Code</label>                    

                        <Select asp-for="PeriodicityCode" class="form-control form-control-sm custom-select" id="PeriodicityCode"  type="text">
    <option></option>
    <option value="Monthly">Monthly</option>
    <option value="Yearly">Yearly</option>
    <option value="Quaterly">Quaterly</option>
</Select>

                    </div>
       
                </div>

                <div class="text-center mt-4">
                    @if (ViewBag.CanModify == true || ViewBag.CanWrite == true)
                    {
                        <button class="btn btn-primary me-2 px-4" id="submitButton" type="submit">Submit</button>
                    }
                    @if (ViewBag.CanDelete == true)
                    {
                        <button class="btn btn-danger px-4" id="deleteButton" style="display:none;">Delete</button>
                    }
                </div>
            </div>
        </div>
    </form>



<script>

    document.addEventListener("DOMContentLoaded", function () {
        var newButton = document.getElementById("newButton");
        var PeriodicityMaster = document.getElementById("form");
        var refNoLinks = document.querySelectorAll(".refNoLink");
        var deleteButton = document.getElementById("deleteButton");
        var submitButton = document.getElementById("submitButton");
        var actionTypeInput = document.getElementById("actionType");

        if (newButton) {
            newButton.addEventListener("click", function () {
                PeriodicityMaster.style.display = "block";
                document.getElementById("PeriodicityCode").value = "";
                document.getElementById("PeriodicityName").value = "";
                document.getElementById("MaximumEntryDate").value = "";            
                document.getElementById("PeriodicityId").value = "";
                deleteButton.style.display = "none";
            });
        }

        refNoLinks.forEach(link => {
            link.addEventListener("click", function (event) {
                event.preventDefault();
                PeriodicityMaster.style.display = "block";

                document.getElementById("PeriodicityCode").value = this.getAttribute("data-PeriodicityCode");
                document.getElementById("PeriodicityName").value = this.getAttribute("data-PeriodicityName");
                document.getElementById("MaximumEntryDate").value = this.getAttribute("data-MaximumEntryDate");
                document.getElementById("PeriodicityId").value = this.getAttribute("data-id");

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
            text: "Do you really want to delete this Periodicity?",
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
