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
