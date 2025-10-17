[HttpGet]
public async Task<JsonResult> GetPeriodicity(Guid periodicityId)
{
    using (var connection = new SqlConnection(GetConnection()))
    {
        string query = @"
            SELECT PeriodicityName 
            FROM App_PeriodicityTransaction
            WHERE PeriodicityID = @PeriodicityID
            ORDER BY Sl_no";

        var result = await connection.QueryAsync<string>(query, new { PeriodicityID = periodicityId });
        return Json(result);
    }
}


<div id="periodicityContainer" class="mt-3">
    <label class="form-label fw-bold">Period Targets</label>
    <div id="periodicityFields" class="row gy-2">
        <!-- JS will insert dynamic input fields here -->
    </div>
</div>

<script>
document.addEventListener('DOMContentLoaded', function () {
    const KPIMaster = document.getElementById("form");
    const refNoLinks = document.querySelectorAll(".refNoLink");
    const deleteButton = document.getElementById("deleteButton");
    const submitButton = document.getElementById("submitButton");
    const actionTypeInput = document.getElementById("actionType");

    refNoLinks.forEach(link => {
        link.addEventListener("click", async function (event) {
            event.preventDefault();
            KPIMaster.style.display = "block";

            // Fill existing fields
            document.getElementById("KPICode").value = this.dataset.kpicode;
            document.getElementById("UnitCode").value = this.dataset.unitcode;
            document.getElementById("KPIDetails").value = this.dataset.kpidetails;
            document.getElementById("KPIID").value = this.dataset.id;

            if (deleteButton) deleteButton.style.display = "inline-block";

            // Fetch Periodicity
            const periodicityId = this.dataset.periodicityid;
            const periodicityContainer = document.getElementById("periodicityFields");

            // Clear old items
            periodicityContainer.innerHTML = `
                <div class='text-muted small'>Loading periods...</div>
            `;

            try {
                const response = await fetch(`/YourControllerName/GetPeriodicity?periodicityId=${periodicityId}`);
                const periods = await response.json();

                if (periods.length > 0) {
                    periodicityContainer.innerHTML = "";
                    periods.forEach(period => {
                        const div = document.createElement("div");
                        div.classList.add("col-md-3");
                        div.innerHTML = `
                            <div class="input-group input-group-sm">
                                <span class="input-group-text">${period}</span>
                                <input type="text" class="form-control" name="Target_${period}" placeholder="Target">
                            </div>
                        `;
                        periodicityContainer.appendChild(div);
                    });
                } else {
                    periodicityContainer.innerHTML = `<div class="text-danger small">No periods found for this KPI.</div>`;
                }

            } catch (error) {
                periodicityContainer.innerHTML = `<div class="text-danger small">Error loading periodicity data.</div>`;
                console.error(error);
            }
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
</script>

#periodicityContainer .input-group-text {
    width: 60px;
    text-align: center;
    background-color: #f8f9fa;
    font-weight: 500;
}




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
