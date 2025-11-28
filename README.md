function toDateTimeLocal(sqlDate) {
    if (!sqlDate) return "";

    // Remove milliseconds if present
    sqlDate = sqlDate.split('.')[0];

    let datePart, timePart;

    if (sqlDate.includes(" ")) {
        [datePart, timePart] = sqlDate.split(" ");
    } else {
        return "";
    }

    // Handle both formats:
    // DD-MM-YYYY
    // MM/DD/YYYY
    let day, month, year;

    if (datePart.includes("-")) {
        const parts = datePart.split("-");
        day = parts[0];
        month = parts[1];
        year = parts[2];
    }
    else if (datePart.includes("/")) {
        const parts = datePart.split("/");
        month = parts[0];
        day = parts[1];
        year = parts[2];
    }
    else {
        return "";
    }

    return `${year}-${month.padStart(2,'0')}-${day.padStart(2,'0')}T${timePart.substring(0,5)}`;
}





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

    let parts = sqlDate.split(" ");
    let date = parts[0];
    let time = parts[1];

    let d = date.split("-");  
    let formatted = `${d[2]}-${d[1]}-${d[0]}T${time.substring(0,5)}`;

    return formatted;
}

function showOnlyQuarter(quarterCode) {
    document.querySelectorAll(".quarter-block").forEach(block => {
        if (block.dataset.quarter === quarterCode) {
            block.style.display = "flex";  
        } else {
            block.style.display = "none";  
        }
    });
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
         let monthlyYearly = document.getElementById("monthlyYearlyFields");
    let quarterly = document.getElementById("quarterlyFields");

        if (newButton) {
            newButton.addEventListener("click", function () {
                PeriodicityMaster.style.display = "block";
                document.getElementById("PeriodicityCode").value = "";          
                document.getElementById("PeriodicityId").value = "";
                document.getElementById("KpiSPOC").value = "";
                document.getElementById("ImmediateSuperior").value = "";
                document.getElementById("HOD").value = "";
                document.getElementById("Q1_KPISPOC").value = "";
                document.getElementById("Q1_ImmediateSuperior").value = "";
                document.getElementById("Q1_HOD").value = "";
                document.getElementById("Q2_KPISPOC").value = "";
                document.getElementById("Q2_ImmediateSuperior").value = "";
                document.getElementById("Q2_HOD").value = "";
                document.getElementById("Q3_KPISPOC").value = "";
                document.getElementById("Q3_ImmediateSuperior").value = "";
                document.getElementById("Q3_HOD").value = "";
                document.getElementById("Q4_KPISPOC").value = "";
                document.getElementById("Q4_ImmediateSuperior").value = "";
                document.getElementById("Q4_HOD").value = "";
                deleteButton.style.display = "none";

                monthlyYearly.style.display = "none";
        quarterly.style.display = "none";

            });
        }

document.querySelectorAll(".refNoLink").forEach(link => {
    link.addEventListener("click", function (e) {
        e.preventDefault();

        PeriodicityMaster.style.display = "block";  

        const periodicity = this.dataset.periodicitycode;
        const category = this.dataset.category;

        const kpiSpoc = this.dataset.kpispoc;
        const immediateSuperior = this.dataset.immediatesuperior;
        const hod = this.dataset.hod;

        document.getElementById("PeriodicityCode").value = periodicity;
        document.getElementById("PeriodicityId").value = this.dataset.id;

        togglePeriodicityFields(); 

        if (periodicity === "Monthly" || periodicity === "Yearly") {
            document.getElementById("KpiSPOC").value = toDateTimeLocal(kpiSpoc);
            document.getElementById("ImmediateSuperior").value = toDateTimeLocal(immediateSuperior);
            document.getElementById("HOD").value = toDateTimeLocal(hod);
        }

        if (periodicity === "Quarterly") {

    const quarterMap = {
        "Apr - Jun (Q1 - 1st Qtr)": "Q1",
        "Jul - Sep (Q2 - 2nd Qtr)": "Q2",
        "Oct - Dec (Q3 - 3rd Qtr)": "Q3",
        "Jan - Mar (Q4 - 4th Qtr)": "Q4"
    };

    const q = quarterMap[category];

    if (q) {

        showOnlyQuarter(q);

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


6The specified value "undefined-undefined-1<URL>" does not conform to the required format.  The format is "yyyy-MM-ddThh:mm" followed by optional ":ss" or ":ss.SSS".Understand this warning
PeriodicityMaster?page=1&searchString=:689 The specified value "undefined-undefined-1/10/2025T10:34" does not conform to the required format.  The format is "yyyy-MM-ddThh:mm" followed by optional ":ss" or ":ss.SSS".
(anonymous) @ PeriodicityMaster?page=1&searchString=:689Understand this warning
PeriodicityMaster?page=1&searchString=:690 The specified value "undefined-undefined-1/20/2025T10:34" does not conform to the required format.  The format is "yyyy-MM-ddThh:mm" followed by optional ":ss" or ":ss.SSS".
(anonymous) @ PeriodicityMaster?page=1&searchString=:690Understand this warning
PeriodicityMaster?page=1&searchString=:691 The specified value "undefined-undefined-1/26/2025T10:34" does not conform to the required format.  The format is "yyyy-MM-ddThh:mm" followed by optional ":ss" or ":ss.SSS".
(anonymous) @ PeriodicityMaster?page=1&searchString=:691Understand this warning
PeriodicityMaster?page=1&searchString=:689 The specified value "undefined-undefined-1/10/2025T10:34" does not conform to the required format.  The format is "yyyy-MM-ddThh:mm" followed by optional ":ss" or ":ss.SSS".
(anonymous) @ PeriodicityMaster?page=1&searchString=:689Understand this warning
PeriodicityMaster?page=1&searchString=:690 The specified value "undefined-undefined-1/20/2025T10:34" does not conform to the required format.  The format is "yyyy-MM-ddThh:mm" followed by optional ":ss" or ":ss.SSS".
(anonymous) @ PeriodicityMaster?page=1&searchString=:690Understand this warning
PeriodicityMaster?page=1&searchString=:691 The specified value "undefined-undefined-1/26/2025T10:34" does not conform to the required format.  The format is "yyyy-MM-ddThh:mm" followed by optional ":ss" or ":ss.SSS".
(anonymous) @ PeriodicityMaster?page=1&searchString=:691Understand this warning
PeriodicityMaster?page=1&searchString=:689 The specified value "undefined-undefined-2/10/2025T10:15" does not conform to the required format.  The format is "yyyy-MM-ddThh:mm" followed by optional ":ss" or ":ss.SSS".
(anonymous) @ PeriodicityMaster?page=1&searchString=:689Understand this warning
PeriodicityMaster?page=1&searchString=:690 The specified value "undefined-undefined-7/21/2025T10:15" does not conform to the required format.  The format is "yyyy-MM-ddThh:mm" followed by optional ":ss" or ":ss.SSS".
(anonymous) @ PeriodicityMaster?page=1&searchString=:690Understand this warning
PeriodicityMaster?page=1&searchString=:691 The specified value "undefined-undefined-11/15/2025T10:15" does not conform to the required format.  The format is "yyyy-MM-ddThh:mm" followed by optional ":ss" or ":ss.SSS".
