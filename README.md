<script>

    function togglePeriodicityFields() {
    let code = document.getElementById("PeriodicityCode").value;
    let monthlyYearly = document.getElementById("monthlyYearlyFields");
    let quarterly = document.getElementById("quarterlyFields");

    if (code === "Monthly" || code === "Yearly") {
        monthlyYearly.style.display = "flex";
        quarterly.style.display = "none";
    }
    else if (code === "Quarterly") {
        monthlyYearly.style.display = "none";
        quarterly.style.display = "block";
    }
    else {
        monthlyYearly.style.display = "none";
        quarterly.style.display = "none";
    }
}

function toDateTimeLocal(sqlDate) {
    if (!sqlDate) return "";

    let cleaned = sqlDate.replace(' ', 'T').split('.')[0];

    const d = new Date(cleaned);

    if (isNaN(d.getTime())) return "";

    return cleaned.slice(0, 16);
}
document.getElementById("PeriodicityCode")
    .addEventListener("change", togglePeriodicityFields);


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
                document.getElementById("PeriodicityId").value = "";
                deleteButton.style.display = "none";
            });
        }

        document.querySelectorAll(".refNoLink").forEach(link => {
    link.addEventListener("click", function (e) {
        e.preventDefault();

        const periodicity = this.dataset.periodicitycode;
        const category = this.dataset.category;

        const kpiSpoc = this.dataset.kpispoc;
        const immediateSuperior = this.dataset.immediatesuperior;
        const hod = this.dataset.hod;

        document.getElementById("PeriodicityCode").value = periodicity;
        document.getElementById("PeriodicityId").value = this.dataset.id;

        // Hide all first
        document.getElementById("monthlyYearlyFields").style.display = "none";
        document.getElementById("quarterlyFields").style.display = "none";

        // ===== MONTHLY / YEARLY =====
        if (periodicity === "Monthly" || periodicity === "Yearly") {
            document.getElementById("monthlyYearlyFields").style.display = "flex";

            document.getElementById("KpiSPOC").value = toDateTimeLocal(kpiSpoc);
            document.getElementById("ImmediateSuperior").value = toDateTimeLocal(immediateSuperior);
            document.getElementById("HOD").value = toDateTimeLocal(hod);
        }

        // ===== QUARTERLY =====
        if (periodicity === "Quarterly") {
            document.getElementById("quarterlyFields").style.display = "block";

            const quarterMap = {
                "Apr - Jun (Q1 - 1st Qtr)": "Q1",
                "Jul - Sep (Q2 - 2nd Qtr)": "Q2",
                "Oct - Dec (Q3 - 3rd Qtr)": "Q3",
                "Jan - Mar (Q4 - 4th Qtr)": "Q4"
            };

            const q = quarterMap[category]; // Q1, Q2, Q3, Q4

            if (q) {
                document.getElementById(`${q}_KPISPOC`).value = toDateTimeLocal(kpiSpoc);
                document.getElementById(`${q}_ImmediateSuperior`).value = toDateTimeLocal(immediateSuperior);
                document.getElementById(`${q}_HOD`).value = toDateTimeLocal(hod);
            }
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
