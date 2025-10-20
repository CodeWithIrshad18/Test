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

            document.getElementById("KPICode").value = this.dataset.kpicode;
            document.getElementById("UnitCode").value = this.dataset.unitcode;
            document.getElementById("KPIDetails").value = this.dataset.kpidetails;
            document.getElementById("KPIID").value = this.dataset.id;
            document.getElementById("PeriodicityID").value = this.dataset.periodicityid;

            if (deleteButton) deleteButton.style.display = "inline-block";

            const periodicityId = this.dataset.periodicityid;
            const periodicityContainer = document.getElementById("periodicityFields");

            periodicityContainer.innerHTML = `
                <div class='text-muted small'>Loading periods...</div>
            `;

            try {
                const response = await fetch(`/TPR/GetPeriodicity?periodicityId=${periodicityId}`);
                const periods = await response.json();

                if (periods.length > 0) {
                    periodicityContainer.innerHTML = "";
                    periods.forEach(period => {
    let colClass = "col-md-3"; 
    const total = periods.length;

    if (total <= 4) {
        colClass = "col-md-6"; 
    } else if (total === 1) {
        colClass = "col-12"; 
    } else if (total <= 6) {
        colClass = "col-md-4";
    }

    const div = document.createElement("div");
    div.className = `${colClass} mb-2`;

    div.innerHTML = `
        <div class="input-group input-group-sm flex-nowrap">
            <span class="input-group-text text-truncate" 
                  style="max-width: 200px;" 
                  title="${period}">
                ${period}
            </span>

            <input type="text" class="form-control" 
                   name="Target_${period.replace(/[^a-zA-Z0-9]/g, '_')}" 
                   placeholder="Target">
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
