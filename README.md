I have already this anchor tag 
   <a href="#"
   class="refNoLink"
   data-id="@item.ID"
   data-KPICode="@item.KPICode"
   data-UnitCode="@item.UnitCode"
   data-KPIDetails="@item.KPIDetails"
   data-PeriodicityID="@item.PeriodicityID"
   data-PeriodicityName="@item.PeriodicityName"
   data-bs-toggle="modal"
        data-bs-target="#periodicityModal">
   @item.KPIDetails
</a>

and this is my js 

document.addEventListener('DOMContentLoaded', function () {
    const newButton = document.getElementById("newButton");
    const KPIMaster = document.getElementById("form");
    const refNoLinks = document.querySelectorAll(".refNoLink");
    const deleteButton = document.getElementById("deleteButton");
    const submitButton = document.getElementById("submitButton");
    const actionTypeInput = document.getElementById("actionType");

    refNoLinks.forEach(link => {
        link.addEventListener("click", function (event) {
            event.preventDefault();
            KPIMaster.style.display = "block";

           document.getElementById("KPICode").value = this.getAttribute("data-KPICode");  
            document.getElementById("UnitCode").value = this.getAttribute("data-UnitCode"); 
            document.getElementById("KPIDetails").value = this.getAttribute("data-KPIDetails");
            document.getElementById("KPIID").value = this.getAttribute("data-id");

            if (deleteButton) deleteButton.style.display = "inline-block";
        });
    });

    if (submitButton) {
        submitButton.addEventListener("click", function () {
            actionTypeInput.value = "save";
        });
    }

    if (deleteButton) {
        deleteButton.addEventListener("click", function () {
            Swal.fire({
                title: 'Are you sure?',
                text: "Do you really want to delete this Unit?",
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


i dont want modal , i want simple good looking form type because there is other forms also 
