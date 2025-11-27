= <a href="#" class="refNoLink"
    data-id="@item.ID"
    data-PeriodicityCode="@item.PeriodicityCode"
    data-PeriodicityName="@item.PeriodicityName"                                       
    data-createdBy="@item.CreatedBy">
     @item.PeriodicityCode
 </a>

dropdown to choose PeriodicityCode
   <Select asp-for="PeriodicityCode" class="form-control form-control-sm custom-select" id="PeriodicityCode"  type="text">
        <option value=""></option>   
    <option value="Monthly">Monthly</option>
    <option value="Yearly">Yearly</option>
    <option value="Quarterly">Quarterly</option>
</Select>


data :

ID	PeriodicityCode	PeriodicityName	KPISPOC	ImmediateSuperior	HOD	Category	CreatedBy	CreatedOn
CDD263FE-5946-4BD2-A2C7-B7E31C19640A	Monthly	Monthly	2025-11-07 10:15:00.000	2025-11-21 10:15:00.000	2025-11-25 10:15:00.000	Monthly	151514	NULL
983CC4F5-339B-4688-9D08-B97200024CA3	Quarterly	Quarterly	2025-11-21 12:04:00.000	2025-11-25 12:04:00.000	2025-11-26 12:04:00.000	Jan - Mar (Q4 - 4th Qtr)	151514	2025-11-27 12:05:07.617
CBDE69E6-B9FB-458D-A04B-C70B16C63C5B	Quarterly	Quarterly	2025-01-23 12:04:00.000	2025-01-20 12:04:00.000	2025-01-31 12:04:00.000	Apr - Jun (Q1 - 1st Qtr)	151514	2025-11-27 12:05:07.617
F1898CE6-E7FA-4726-9E1B-680BA4F981F9	Quarterly	Quarterly	2025-11-18 12:04:00.000	2025-11-25 12:04:00.000	2025-11-22 12:04:00.000	Oct - Dec (Q3 - 3rd Qtr)	151514	2025-11-27 12:05:07.617
A0CCBD90-449E-40C6-BDD8-82A6B67D0C29	Quarterly	Quarterly	2025-11-06 12:04:00.000	2025-11-16 12:04:00.000	2025-11-19 12:04:00.000	Jul - Sep (Q2 - 2nd Qtr)	151514	2025-11-27 12:05:07.617
D16070D5-69F0-402B-BA51-8D2909BECA11	Yearly	Yearly	2025-11-11 10:34:00.000	2025-11-18 10:34:00.000	2025-11-26 10:34:00.000	Yearly	151514	NULL


Js:

        refNoLinks.forEach(link => {
            link.addEventListener("click", function (event) {
                event.preventDefault();
                PeriodicityMaster.style.display = "block";

                document.getElementById("PeriodicityCode").value = this.getAttribute("data-PeriodicityCode");        
                document.getElementById("PeriodicityId").value = this.getAttribute("data-id");

                if (deleteButton) {
                    deleteButton.style.display = "inline-block";
                }
            });
        });


<a href="#" class="refNoLink" data-id="f1898ce6-e7fa-4726-9e1b-680ba4f981f9" data-periodicitycode="Quarterly" data-periodicityname="Quarterly" data-category="Oct - Dec (Q3 - 3rd Qtr)" data-createdby="151514">
                                        Quarterly
                                    </a>

for monthly and yearly
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

quarterly 

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


I want when clicking on refNoLink populate the data from the data-bs of anchor tag. and shows in the textboxes. monthly and yearly is simple and for quarter show against the category
