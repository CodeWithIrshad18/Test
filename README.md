data-deactivatefrom="02-01-2026 19:19:00"

data-deactivateto="31-01-2030 19:19:00"


function formatDateForInput(dateString) {
    if (!dateString) return "";

    const d = new Date(dateString);
    if (isNaN(d.getTime())) return "";

    const pad = n => n.toString().padStart(2, "0");

    return `${d.getFullYear()}-${pad(d.getMonth() + 1)}-${pad(d.getDate())}T${pad(d.getHours())}:${pad(d.getMinutes())}`;
}


  document.getElementById("DeactivateFrom").value = formatDateForInput(deactivateFrom);
  document.getElementById("DeactivateTo").value = formatDateForInput(deactivateTo);





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


PeriodicityMaster:714 The specified value "2026-01-10T3:59:" does not conform to the required format.  The format is "yyyy-MM-ddThh:mm" followed by optional ":ss" or ":ss.SSS".
(anonymous) @ PeriodicityMaster:714Understand this warning
PeriodicityMaster:715 The specified value "2025-12-20T3:59:" does not conform to the required format.  The format is "yyyy-MM-ddThh:mm" followed by optional ":ss" or ":ss.SSS".
(anonymous) @ PeriodicityMaster:715Understand this warning
PeriodicityMaster:716 The specified value "2025-12-28T3:59:" does not conform to the required format.  The format is "yyyy-MM-ddThh:mm" followed by optional ":ss" or ":ss.SSS".
