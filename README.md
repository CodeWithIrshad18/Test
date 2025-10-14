refNoLinks.forEach(link => {
    link.addEventListener("click", function (event) {
        event.preventDefault();
        KPIMaster.style.display = "block";

        document.getElementById("KPICode").value = this.getAttribute("data-KPICode");
        document.getElementById("KPILevel").value = this.getAttribute("data-KPILevel");
        document.getElementById("Company").value = this.getAttribute("data-Company");
        document.getElementById("Division").value = this.getAttribute("data-Division");
        document.getElementById("Department").value = this.getAttribute("data-Department");
        document.getElementById("Section").value = this.getAttribute("data-Section");
        document.getElementById("PerspectiveID").value = this.getAttribute("data-PerspectiveID");
        document.getElementById("TypeofKPIID").value = this.getAttribute("data-TypeofKPIID");
        document.getElementById("UnitID").value = this.getAttribute("data-UnitID");
        document.getElementById("KPIDefination").value = this.getAttribute("data-KPIDefination");
        document.getElementById("KPIDetails").value = this.getAttribute("data-KPIDetails");
        document.getElementById("PeriodicityID").value = this.getAttribute("data-PeriodicityName");
        document.getElementById("GoodPerformance").value = this.getAttribute("data-GoodPerformance");
        document.getElementById("NoofDecimal").value = this.getAttribute("data-NoofDecimal");
        document.getElementById("KPIID").value = this.getAttribute("data-id");

        // ✅ Check Division checkboxes based on data-Division
        const divisionValues = this.getAttribute("data-Division")?.split(';').map(v => v.trim()) || [];
        document.querySelectorAll('.division-checkbox').forEach(cb => {
            cb.checked = divisionValues.includes(cb.value.trim());
        });

        // ✅ Update dropdown and hidden input
        const divisionInput = document.getElementById('divisionDropdown');
        const divisionHidden = document.getElementById('Division');
        const selectedDivisions = Array.from(document.querySelectorAll('.division-checkbox:checked')).map(cb => cb.value);
        divisionHidden.value = selectedDivisions.join(';');
        divisionInput.value = selectedDivisions.length ? `${selectedDivisions.length} selected` : '';

        if (deleteButton) {
            deleteButton.style.display = "inline-block";
        }
    });
});



and now this is my edit js 
        refNoLinks.forEach(link => {
    link.addEventListener("click", function (event) {
        event.preventDefault();
        KPIMaster.style.display = "block";

        document.getElementById("KPICode").value = this.getAttribute("data-KPICode");
        document.getElementById("KPILevel").value = this.getAttribute("data-KPILevel");
        document.getElementById("Company").value = this.getAttribute("data-Company");
        document.getElementById("Division").value = this.getAttribute("data-Division");
        document.getElementById("Department").value = this.getAttribute("data-Department");
        document.getElementById("Section").value = this.getAttribute("data-Section");
        document.getElementById("PerspectiveID").value = this.getAttribute("data-PerspectiveID");
        document.getElementById("TypeofKPIID").value = this.getAttribute("data-TypeofKPIID");
        document.getElementById("UnitID").value = this.getAttribute("data-UnitID");
        document.getElementById("KPIDefination").value = this.getAttribute("data-KPIDefination");
        document.getElementById("KPIDetails").value = this.getAttribute("data-KPIDetails");
        document.getElementById("PeriodicityID").value = this.getAttribute("data-PeriodicityName");
        document.getElementById("GoodPerformance").value = this.getAttribute("data-GoodPerformance");
        document.getElementById("NoofDecimal").value = this.getAttribute("data-NoofDecimal");
        document.getElementById("KPIID").value = this.getAttribute("data-id");

        if (deleteButton) {
            deleteButton.style.display = "inline-block";
        }
    });
});

in data-division i have this, i want to show checkboxes checked according to values 

data-division="People Function;Power Services & Utilities Billing;Procurement"
