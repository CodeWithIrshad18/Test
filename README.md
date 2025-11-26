01/10/2025	spoc		monthly
20/01/2025	supp		monthly
28/01/2025	hod		monthly
01/10/2025	spoc		yearly
20/01/2025	supp		yearly
28/01/2025	hod		yearly
04/10/2025	spoc		q1
20/04/2025	supp		q1
28/04/2025	hod		q1
07/10/2025	spoc		q2
20/07/2025	supp		q2
28/07/2025	hod		q2
10/10/2025	spoc		q3
20/10/2025	supp		q3
28/10/2025	hod		q3
01/10/2025	spoc		q4
20/01/2025	supp		q4
28/01/2025	hod		q4




 public class AppPeriodicityMaster
 {

     public Guid ID { get; set; }
     public string? PeriodicityCode { get; set; }
     public string? PeriodicityName { get; set; }
     public string? CreatedBy { get; set; }
     public DateTime? KPISPOC { get; set; }
     public DateTime? ImmediateSuperior { get; set; }
     public DateTime? HOD { get; set; }
     public string? Category { get; set; }
     public DateTime? CreatedOn { get; set; }
 }


    <form asp-action="PeriodicityMaster" asp-controller="Master" id="form" method="post" style="display:none;">
        <div class="card card-custom mt-4">
            <div class="card-header-custom">Periodicity Master</div>
            <div class="card-body">
                <div class="row g-3">
                    <input type="hidden" asp-for="ID" id="PeriodicityId" name="ID" />
                    <input type="hidden" id="actionType" name="actionType" />

                    <div class="col-md-3">
                        <label for="PeriodicityCode" class="control-label">Periodicity Code</label>                    

   <Select asp-for="PeriodicityCode" class="form-control form-control-sm custom-select" id="PeriodicityCode"  type="text">
        <option value=""></option>   
    <option value="Monthly">Monthly</option>
    <option value="Yearly">Yearly</option>
    <option value="Quaterly">Quaterly</option>
</Select>
                 </div>
 
        <div class="col-md-3">
            <label class="control-label">KPI SPOC,Date</label>
            <input type="datetime-local" class="form-control form-control-sm" id="KpiSPOC" name="KPISPOC_DateTime">
        </div>

        <div class="col-md-3">
            <label class="control-label">Immediate Superior,Date</label>
            <input type="datetime-local" class="form-control form-control-sm" id="ImmediateSuperior" name="ImmediateSuperior_DateTime">
        </div>

        <div class="col-md-3">
            <label class="control-label">HOD,Date</label>
            <input type="datetime-local" class="form-control form-control-sm" id="HOD" name="HOD_DateTime">
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
