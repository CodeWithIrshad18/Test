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
        document.getElementById("DeactiveFrom").value = this.getAttribute("data-DeactivateFrom");
        document.getElementById("DeactiveTo").value = this.getAttribute("data-DeactivateTo");
        document.getElementById("KPIID").value = this.getAttribute("data-id");

        // ✅ NEW CODE: Handle Active/Inactive
        const deactivate = this.getAttribute("data-Deactivate");
        const deactivateFrom = this.getAttribute("data-DeactivateFrom");
        const deactivateTo = this.getAttribute("data-DeactivateTo");

        const activeRadio = document.getElementById("Active");
        const inactiveRadio = document.getElementById("Inactive");
        const deactiveFields = document.querySelectorAll(".deactive-field");

        if (deactivate === "true") {
            inactiveRadio.checked = true;
            activeRadio.checked = false;
            deactiveFields.forEach(f => f.style.display = "block");
            document.getElementById("DeactiveFrom").value = deactivateFrom || "";
            document.getElementById("DeactiveTo").value = deactivateTo || "";
        } else {
            activeRadio.checked = true;
            inactiveRadio.checked = false;
            deactiveFields.forEach(f => f.style.display = "none");
            document.getElementById("DeactiveFrom").value = "";
            document.getElementById("DeactiveTo").value = "";
        }

        // ✅ Your existing logic continues here for divisions/departments
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

// === Handle manual toggle of Active / Inactive ===
const activeRadio = document.getElementById("Active");
const inactiveRadio = document.getElementById("Inactive");
const deactiveFields = document.querySelectorAll(".deactive-field");

// Function to show/hide date fields dynamically
function updateDeactivateFields() {
    if (inactiveRadio.checked) {
        deactiveFields.forEach(f => f.style.display = "block");
    } else {
        deactiveFields.forEach(f => f.style.display = "none");
        document.getElementById("DeactiveFrom").value = "";
        document.getElementById("DeactiveTo").value = "";
    }
}

// Attach event listeners
activeRadio.addEventListener("change", updateDeactivateFields);
inactiveRadio.addEventListener("change", updateDeactivateFields);

// Initialize on page load (in case form preloaded)
updateDeactivateFields();




this is my full code i want to fetch Active/InActive when refNolink click
  data-Deactivate="@item.Deactivate"
   data-DeactiveFrom="@item.DeactivateFrom"
   data-DeactiveTo="@item.DeactivateTo"

        SELECT 
    k.ID,
    k.KPIDetails,
    k.KPISPOC,
    k.ImmediateSuperior,
    k.Deactivate,
    k.DeactivateFrom,
    k.DeactivateTo,
    k.HOD,
    ps.Perspectives,
    u.UnitCode,
    k.KPILevel,
    p.PeriodicityName,
    k.Division,
    k.Department,
    k.Section,
    g.Name,
    k.KPICode,
    k.TypeofKPIID,
    k.KPIDefination,
    k.Company,
    k.GoodPerformance,
    k.PeriodicityID,
    k.UnitID,
    k.PerspectiveID,
    k.NoofDecimal
FROM App_KPIMaster_NOPR k
LEFT JOIN App_PeriodicityMaster_NOPR p ON k.PeriodicityID = p.ID
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
LEFT JOIN App_Prespectives_NOPR ps ON k.PerspectiveID = ps.ID
LEFT JOIN App_TypeofKPI_NOPR t ON k.TypeofKPIID = t.ID
LEFT JOIN App_GoodPerformance_NOPR g ON k.GoodPerformance = g.ID
WHERE
    (@search IS NULL OR k.KPICode LIKE '%' + @search + '%' OR u.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%')
AND (@search3 IS NULL OR k.KPIDetails LIKE '%' + @search3 + '%')
ORDER BY k.KPIDetails
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;



                              <div class="row g-3 mt-1 align-items-center">
    <div class="col-md-2">
        <label for="Mode" class="control-label">Mode</label>
    </div>

    <div class="col-md-1">
        <div class="form-check">
            <input type="radio" class="form-check-input" name="Deactivate" id="Active" value="false" autocomplete="off" checked>
            <label class="form-check-label" for="Active">Active</label>
        </div>
    </div>

    <div class="col-md-1">
        <div class="form-check">
            <input type="radio" class="form-check-input" name="Deactivate" id="Inactive" value="true" autocomplete="off">
            <label class="form-check-label" for="Inactive">Inactive</label>
        </div>
    </div>

    <div class="col-md-2 deactive-field" style="display: none;">
        <label for="DeactiveFrom" class="control-label">Deactive From</label>
    </div>

    <div class="col-md-2 deactive-field" style="display: none;">
        <input type="datetime-local" class="form-control form-control-sm" id="DeactiveFrom" name="DeactivateFrom">
    </div>

    <div class="col-md-2 deactive-field" style="display: none;">
        <label for="DeactiveTo" class="control-label">Deactive To</label>
    </div>

    <div class="col-md-2 deactive-field" style="display: none;">
        <input type="datetime-local" class="form-control form-control-sm" id="DeactiveTo" name="DeactivateTo">
    </div>
</div>




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
            document.getElementById("DeactiveFrom").value = this.getAttribute("data-DeactivateFrom");
            document.getElementById("DeactiveTo").value = this.getAttribute("data-DeactivateTo");
            document.getElementById("KPIID").value = this.getAttribute("data-id");

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
