function toDateTimeLocalSafe(dateString) {
    if (!dateString) return "";

    // Expected: dd-MM-yyyy HH:mm:ss OR dd-MM-yyyy HH:mm
    const match = dateString.match(
        /^(\d{2})-(\d{2})-(\d{4}) (\d{2}):(\d{2})/
    );

    if (!match) return "";

    const [, day, month, year, hour, minute] = match;

    return `${year}-${month}-${day}T${hour}:${minute}`;
}

document.getElementById("DeactivateFrom").value =
    toDateTimeLocalSafe(deactivateFrom);

document.getElementById("DeactivateTo").value =
    toDateTimeLocalSafe(deactivateTo);

document.getElementById("KpiSPOC").value =
    toDateTimeLocalSafe(kpiSpoc);

document.getElementById("ImmediateSuperior").value =
    toDateTimeLocalSafe(immediateSuperior);

document.getElementById("HOD").value =
    toDateTimeLocalSafe(hod);


document.getElementById(`${q}_KPISPOC`).value =
    toDateTimeLocalSafe(kpiSpoc);



/* ===========================
   Global Date Utility
   =========================== */

window.DateUtil = {

    /**
     * Converts SQL / UI date strings to HTML datetime-local format
     * Supports:
     *  - dd-MM-yyyy HH:mm:ss
     *  - dd-MM-yyyy HH:mm
     *  - dd/MM/yyyy HH:mm:ss
     *  - yyyy-MM-dd HH:mm:ss
     * Returns:
     *  - yyyy-MM-ddTHH:mm (datetime-local safe)
     */
    toDateTimeLocal: function (dateString) {
        if (!dateString || typeof dateString !== "string") return "";

        // Trim & normalize
        dateString = dateString.trim();

        let match;

        // dd-MM-yyyy HH:mm(:ss)
        match = dateString.match(
            /^(\d{2})[-/](\d{2})[-/](\d{4}) (\d{1,2}):(\d{2})/
        );
        if (match) {
            const [, day, month, year, hour, minute] = match;
            return `${year}-${month}-${day}T${hour.padStart(2, '0')}:${minute}`;
        }

        // yyyy-MM-dd HH:mm(:ss)
        match = dateString.match(
            /^(\d{4})-(\d{2})-(\d{2}) (\d{1,2}):(\d{2})/
        );
        if (match) {
            const [, year, month, day, hour, minute] = match;
            return `${year}-${month}-${day}T${hour.padStart(2, '0')}:${minute}`;
        }

        // Already ISO (safe)
        match = dateString.match(
            /^(\d{4}-\d{2}-\d{2})T(\d{2}:\d{2})/
        );
        if (match) {
            return `${match[1]}T${match[2]}`;
        }

        // Invalid / unsupported format
        return "";
    },

    /**
     * Validates datetime-local value
     */
    isValidDateTimeLocal: function (value) {
        return /^\d{4}-\d{2}-\d{2}T\d{2}:\d{2}$/.test(value);
    },

    /**
     * Clears datetime-local input safely
     */
    clear: function (inputId) {
        const el = document.getElementById(inputId);
        if (el) el.value = "";
    },

    /**
     * Sets datetime-local value safely
     */
    set: function (inputId, dateString) {
        const el = document.getElementById(inputId);
        if (!el) return;

        const formatted = this.toDateTimeLocal(dateString);
        el.value = this.isValidDateTimeLocal(formatted) ? formatted : "";
    }
};

DateUtil.set("DeactivateFrom", deactivateFrom);
DateUtil.set("DeactivateTo", deactivateTo);


DateUtil.set("KpiSPOC", kpiSpoc);
DateUtil.set("ImmediateSuperior", immediateSuperior);
DateUtil.set("HOD", hod);


DateUtil.set(`${q}_KPISPOC`, kpiSpoc);
DateUtil.set(`${q}_ImmediateSuperior`, immediateSuperior);
DateUtil.set(`${q}_HOD`, hod);

DateUtil.clear("DeactivateFrom");
DateUtil.clear("DeactivateTo");





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
