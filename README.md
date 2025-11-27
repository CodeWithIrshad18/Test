<script>
function isVisible(el) {
    return !!( el.offsetHeight || el.offsetWidth || el.getClientRects().length );
}

document.getElementById('form').addEventListener('submit', function (event) {
    event.preventDefault();

    let isValid = true;

    let monthlyBlock = document.getElementById("monthlyYearlyFields");
    let quarterlyBlock = document.getElementById("quarterlyFields");

    let elements = [];

    // ✔ validate only visible block
    if (isVisible(monthlyBlock)) {
        elements = monthlyBlock.querySelectorAll('input');
    }
    else if (isVisible(quarterlyBlock)) {
        elements = quarterlyBlock.querySelectorAll('input');
    }

    elements.forEach(function (element) {

        if (element.id === 'PeriodicityId' || element.id === 'CreatedBy') return;

        if (element.value.trim() === '') {
            isValid = false;
            element.classList.add('is-invalid');

            // show message in <span> if available
            let errorSpan = element.closest('div').querySelector('.error-msg');
            if (errorSpan) errorSpan.textContent = "This field is required";
        }
        else {
            element.classList.remove('is-invalid');

            // clear message
            let errorSpan = element.closest('div').querySelector('.error-msg');
            if (errorSpan) errorSpan.textContent = "";
        }
    });

    if (isValid) {
        this.submit();
    }
});
</script>

    
    <form asp-action="PeriodicityMaster" asp-controller="Master" id="form" method="post" style="display:none;">
        <div class="card card-custom mt-4">
            <div class="card-header-custom">Periodicity Master</div>
            <div class="card-body">
                <div class="row g-3">
                    <input type="hidden" asp-for="ID" id="PeriodicityId" name="ID" />
                    <input type="hidden" id="actionType" name="actionType" />

                    <div class="col-md-3">
                        <label for="PeriodicityCode" class="control-label">Periodicity Code</label>                    

   <Select asp-for="PeriodicityCode" class="form-control form-control-sm custom-select" id="PeriodicityCode"  type="text">
        <option value=""></option>   
    <option value="Monthly">Monthly</option>
    <option value="Yearly">Yearly</option>
    <option value="Quarterly">Quarterly</option>
</Select>
                 </div>
 
       <div id="monthlyYearlyFields" class="row g-3 mt-2" style="display:none;">
    <div class="col-md-3">
        <label class="control-label" for="KpiSPOC">KPI SPOC Date</label>
        <input type="datetime-local" class="form-control form-control-sm" id="KpiSPOC" name="KPISPOC">
    </div>

    <div class="col-md-3">
        <label class="control-label" for="ImmediateSuperior">Immediate Superior Date</label>
        <input type="datetime-local" class="form-control form-control-sm" id="ImmediateSuperior" name="ImmediateSuperior">
    </div>

    <div class="col-md-3">
        <label class="control-label" for="HOD">HOD Date</label>
        <input type="datetime-local" class="form-control form-control-sm" id="HOD" name="HOD">
    </div>



 </div>
 <div id="quarterlyFields" style="display:none;">
    <!-- Q1 -->
   <div class="row g-3 mt-1">
         <div class="col-md-2">
             <label class="control-label">Quarter 1</label>
             </div>
        <div class="col-md-1">
             <label class="control-label" for="Q1_KPISPOC">KPI SPOC</label>
            </div>
              <div class="col-md-2">
           
            <input type="datetime-local" class="form-control form-control-sm" name="Q1_KPISPOC" id="Q1_KPISPOC">
            <span class="text-danger small error-msg" data-for="Q1_KPISPOC"></span>
        </div>
             <div class="col-md-1">
                  <label class="control-label" for="Q1_ImmediateSuperior">Immediate Superior</label>
            </div>
             <div class="col-md-2">
           
            <input type="datetime-local" class="form-control form-control-sm" name="Q1_ImmediateSuperior" id="Q1_ImmediateSuperior">
            <span class="text-danger small error-msg" data-for="Q1_ImmediateSuperior"></span>
        </div>
             <div class="col-md-1">
                 <label class="control-label" for="Q1_HOD">HOD</label>
            </div>

        <div class="col-md-2">
            <input type="datetime-local" class="form-control form-control-sm" name="Q1_HOD" id="Q1_HOD">
            <span class="text-danger small error-msg" data-for="Q1_HOD"></span>
        </div>
    </div>

    <!-- Q2 -->
   <div class="row g-3 mt-1">
         <div class="col-md-2">
             <label class="control-label">Quarter 2</label>
             </div>
        <div class="col-md-1">
             <label class="control-label" for="Q2_KPISPOC">KPI SPOC</label>
            </div>
              <div class="col-md-2">
           
            <input type="datetime-local" class="form-control form-control-sm" name="Q2_KPISPOC" id="Q2_KPISPOC">
            <span class="text-danger small error-msg" data-for="Q2_KPISPOC"></span>
        </div>
             <div class="col-md-1">
                  <label class="control-label" for="Q2_ImmediateSuperior">Immediate Superior</label>
            </div>
             <div class="col-md-2">
           
            <input type="datetime-local" class="form-control form-control-sm" name="Q2_ImmediateSuperior" id="Q2_ImmediateSuperior">
            <span class="text-danger small error-msg" data-for="Q2_ImmediateSuperior"></span>
        </div>
             <div class="col-md-1">
                 <label class="control-label" for="Q2_HOD">HOD</label>
            </div>

        <div class="col-md-2">
            <input type="datetime-local" class="form-control form-control-sm" name="Q2_HOD" id="Q2_HOD">
            <span class="text-danger small error-msg" data-for="Q2_HOD"></span>
        </div>
    </div>

    <!-- Q3 -->
   <div class="row g-3 mt-1">
         <div class="col-md-2">
             <label class="control-label">Quarter 3</label>
             </div>
        <div class="col-md-1">
             <label class="control-label" for="Q3_KPISPOC">KPI SPOC</label>
            </div>
              <div class="col-md-2">
           
            <input type="datetime-local" class="form-control form-control-sm" name="Q3_KPISPOC" id="Q3_KPISPOC">
            <span class="text-danger small error-msg" data-for="Q3_KPISPOC"></span>
        </div>
             <div class="col-md-1">
                  <label class="control-label" for="Q3_ImmediateSuperior">Immediate Superior</label>
            </div>
             <div class="col-md-2">
           
            <input type="datetime-local" class="form-control form-control-sm" name="Q3_ImmediateSuperior" id="Q3_ImmediateSuperior">
            <span class="text-danger small error-msg" data-for="Q3_ImmediateSuperior"></span>
        </div>
             <div class="col-md-1">
                 <label class="control-label" for="Q3_HOD">HOD</label>
            </div>

        <div class="col-md-2">
            <input type="datetime-local" class="form-control form-control-sm" name="Q3_HOD" id="Q3_HOD">
            <span class="text-danger small error-msg" data-for="Q3_HOD"></span>
        </div>
    </div>

    <!-- Q4 -->
    <div class="row g-3 mt-1">
         <div class="col-md-2">
             <label class="control-label">Quarter 4</label>
             </div>
        <div class="col-md-1">
             <label class="control-label" for="Q4_KPISPOC">KPI SPOC</label>
            </div>
              <div class="col-md-2">
           
            <input type="datetime-local" class="form-control form-control-sm" name="Q4_KPISPOC" id="Q4_KPISPOC">
            <span class="text-danger small error-msg" data-for="Q4_KPISPOC"></span>
        </div>
             <div class="col-md-1">
                  <label class="control-label" for="Q4_ImmediateSuperior">Immediate Superior</label>
            </div>
             <div class="col-md-2">
           
            <input type="datetime-local" class="form-control form-control-sm" name="Q4_ImmediateSuperior" id="Q4_ImmediateSuperior">
            <span class="text-danger small error-msg" data-for="Q4_ImmediateSuperior"></span>
        </div>
             <div class="col-md-1">
                 <label class="control-label" for="Q4_HOD">HOD</label>
            </div>

        <div class="col-md-2">
            <input type="datetime-local" class="form-control form-control-sm" name="Q4_HOD" id="Q4_HOD">
            <span class="text-danger small error-msg" data-for="Q4_HOD"></span>
        </div>
    </div>
</div>


                <div class="text-center mt-4">
                    @if (ViewBag.CanModify == true || ViewBag.CanWrite == true)
                    {
                        <button class="btn btn-primary me-2 px-4" id="submitButton" type="submit">Submit</button>
                    }
                    @if (ViewBag.CanDelete == true)
                    {
                        <button class="btn btn-danger px-4" id="deleteButton" style="display:none;">Delete</button>
                    }
                </div>
            </div>
        </div>
    </form>


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

<script>
document.getElementById('form').addEventListener('submit', function (event) {
    event.preventDefault();

    let isValid = true;

    let monthlyBlock = document.getElementById("monthlyYearlyFields");
    let quarterlyBlock = document.getElementById("quarterlyFields");

    let elements;

    if (monthlyBlock.offsetParent !== null) {
        elements = monthlyBlock.querySelectorAll('input, select, textarea');
    }
    else if (quarterlyBlock.offsetParent !== null) {
        elements = quarterlyBlock.querySelectorAll('input, select, textarea');
    } 
    else {
        elements = [];
    }

    elements.forEach(function (element) {

        if (element.id === 'PeriodicityId' || element.id === 'CreatedBy') return;

        if (element.value.trim() === '') {
            isValid = false;
            element.classList.add('is-invalid');
        }
        else {
            element.classList.remove('is-invalid');
        }
    });

    if (isValid) {
        this.submit();
    }
});
</script>
