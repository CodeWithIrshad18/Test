function formatDateForInput(dateString) {
    if (!dateString) return "";

    const d = new Date(dateString);
    if (isNaN(d.getTime())) return "";

    const pad = n => n.toString().padStart(2, "0");

    return `${d.getFullYear()}-${pad(d.getMonth() + 1)}-${pad(d.getDate())}T${pad(d.getHours())}:${pad(d.getMinutes())}`;
}





refNoLinks.forEach(link => {
    link.addEventListener("click", function (event) {
        event.preventDefault();
        KPIMaster.style.display = "block";

        document.getElementById("KPICode").value = this.getAttribute("data-KPICode");
        document.getElementById("KPILevel").value = this.getAttribute("data-KPILevel");
        document.getElementById("Company").value = this.getAttribute("data-Company");
        document.getElementById("PerspectiveID").value = this.getAttribute("data-PerspectiveID");
        document.getElementById("TypeofKPIID").value = this.getAttribute("data-TypeofKPIID");
        document.getElementById("UnitID").value = this.getAttribute("data-UnitID");
        document.getElementById("KPIDefination").value = this.getAttribute("data-KPIDefination");
        document.getElementById("KPIDetails").value = this.getAttribute("data-KPIDetails");
        document.getElementById("PeriodicityID").value = this.getAttribute("data-PeriodicityName");
        document.getElementById("GoodPerformance").value = this.getAttribute("data-GoodPerformance");
        document.getElementById("KPISPOC").value = this.getAttribute("data-KPISPOC");
        document.getElementById("ImmediateSuperior").value = this.getAttribute("data-ImmediateSuperior");
        document.getElementById("HOD").value = this.getAttribute("data-HOD");
        document.getElementById("NoofDecimal").value = this.getAttribute("data-NoofDecimal");
        document.getElementById("KPIID").value = this.getAttribute("data-id");

        const deactivate = this.getAttribute("data-deactivate");
const deactivateFrom = this.getAttribute("data-deactivatefrom");
const deactivateTo = this.getAttribute("data-deactivateto");

const activeRadio = document.getElementById("Active");
const inactiveRadio = document.getElementById("Inactive");
const deactiveFields = document.querySelectorAll(".deactive-field");

function formatDateForInput(dateString) {
    if (!dateString) return "";
    const parts = dateString.split(/[- :]/);
    if (parts.length < 5) return "";
    const [day, month, year, hour, minute] = parts;
    return `${year}-${month}-${day}T${hour}:${minute}`;
}

if (deactivate === "true" || deactivate === "True") {
    inactiveRadio.checked = true;
    activeRadio.checked = false;
    deactiveFields.forEach(f => f.style.display = "block");
    document.getElementById("DeactivateFrom").value = formatDateForInput(deactivateFrom);
    document.getElementById("DeactivateTo").value = formatDateForInput(deactivateTo);
} else {
    activeRadio.checked = true;
    inactiveRadio.checked = false;
    deactiveFields.forEach(f => f.style.display = "none");
    document.getElementById("DeactivateFrom").value = "";
    document.getElementById("DeactivateTo").value = "";
}

        const divisionValues = this.getAttribute("data-Division")?.split(';').map(v => v.trim()) || [];
        const departmentValues = this.getAttribute("data-Department")?.split(';').map(v => v.trim()) || [];
        const sectionValues = this.getAttribute("data-Section")?.split(';').map(v => v.trim()) || [];

        document.querySelectorAll('.division-checkbox').forEach(cb => {
            cb.checked = divisionValues.includes(cb.value.trim());
        });
        divisionHidden.value = divisionValues.join(';');
        divisionInput.value = divisionValues.length ? `${divisionValues.length} selected` : '';

        loadDepartments(false, () => {
            document.querySelectorAll('.department-checkbox').forEach(cb => {
                cb.checked = departmentValues.includes(cb.value.trim());
            });
            departmentHidden.value = departmentValues.join(';');
            departmentInput.value = departmentValues.length ? `${departmentValues.length} selected` : '';

            loadSections(false, () => {
                document.querySelectorAll('.section-checkbox').forEach(cb => {
                    cb.checked = sectionValues.includes(cb.value.trim());
                });
                sectionHidden.value = sectionValues.join(';');
                sectionInput.value = sectionValues.length ? `${sectionValues.length} selected` : '';
            });
        });

        if (deleteButton) deleteButton.style.display = "inline-block";
    });
});
