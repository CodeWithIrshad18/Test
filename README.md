  
data is not populating why?


data :
<a href="#" class="refNoLink" data-id="f1898ce6-e7fa-4726-9e1b-680ba4f981f9" data-periodicitycode="Quarterly" data-periodicityname="Quarterly" data-category="Oct - Dec (Q3 - 3rd Qtr)" data-kpispoc="18-11-2025 12:04:00" data-immediatesuperior="25-11-2025 12:04:00" data-hod="22-11-2025 12:04:00" data-createdby="151514">
   Quarterly
</a>

view side:

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
