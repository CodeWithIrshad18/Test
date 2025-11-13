this is my two radio buttons , if yes then shows all the external-comparative-group  if No then Hide all        
     <div class="row g-3 mt-1 align-items-center">
<div class="col-md-2">
    <label for="comparative" class="control-label">Comparative</label>
</div>

<div class="col-md-1">
    <div class="form-check">
        <input type="radio" class="form-check-input" name="Deactivate" id="Yes" value="false" autocomplete="off" checked>
        <label class="form-check-label" for="Yes">Yes</label>
    </div>
</div>

<div class="col-md-1">
    <div class="form-check">
        <input type="radio" class="form-check-input" name="Deactivate" id="No" value="true" autocomplete="off">
        <label class="form-check-label" for="No">No</label>
    </div>
</div>
</div>


                  <div class="external-comparative-group">
                   <div class="row g-3 mt-1">
                  <div class="col-md-2">
                     <label for="Relevant_comparative_available" class="control-label">External comparative</label>
                     </div>

                 <div class="col-md-2">
                    
                     <select asp-for="Targets.Relevant_comparative_available" class="form-control form-control-sm custom-select external-comparative-select" id="Relevant_comparative_available">
                         <option></option>
                         <option value="yes">Available</option>
                         <option value="No">Not Available</option>                           
                     </select>
                 </div>
                  <div class="col-md-1">

                          </div>
                          <div class="col-md-1">

                          </div>
                 <div class="col-md-2">
                     <label for="Relevant_comparative_available_Value" class="control-label">External comparative Value</label>
                     </div>
                     
                 <div class="col-md-1">
                    
                    <input asp-for="Targets.Relevant_comparative_available_Value" class="form-control form-control-sm comparative-value" id="Relevant_comparative_available_Value" autocomplete="off">
                 </div>

                 <div class="col-md-1">
                     <label for="Relevant_comparative_available_Details" class="control-label">Details</label>
                     </div>

                 <div class="col-md-2">
                    <input asp-for="Targets.Relevant_comparative_available_Details" class="form-control form-control-sm comparative-details" id="Relevant_comparative_available_Details" autocomplete="off">
                 </div>
                 </div>
                 </div>
               <div class="external-comparative-group">
                  <div class="row g-3 mt-1">
                  <div class="col-md-2">
                     <label for="Current_performance_better_than_comparative" class="control-label">Current performance</label>
                     </div>

                 <div class="col-md-2">
                    
                     <select asp-for="Targets.Current_performance_better_than_comparative" class="form-control form-control-sm custom-select external-comparative-select" id="Current_performance_better_than_comparative">        
                          <option></option> 
                         <option value="yes">Available</option>                            
                          <option value="No">Not Available</option>                             
                     </select>
                 </div>
                  <div class="col-md-1">

                          </div>
                          <div class="col-md-1">

                          </div>
                 <div class="col-md-2">
                     <label for="Current_performance_better_than_comparative_Value" class="control-label">Current performance Value</label>
                     </div>

                 <div class="col-md-1">
                    
                    <input asp-for="Targets.Current_performance_better_than_comparative_Value" class="form-control form-control-sm comparative-value" id="Current_performance_better_than_comparative_Value" autocomplete="off">
                 </div>
                 <div class="col-md-1">
                     <label for="Current_performance_better_than_comparative_Details" class="control-label">Details</label>
                     </div>

                 <div class="col-md-2">
                    <input asp-for="Targets.Current_performance_better_than_comparative_Details" class="form-control form-control-sm comparative-details" id="Current_performance_better_than_comparative_Details" autocomplete="off">
                 </div>
                 </div>
                 </div>
                  <div class="external-comparative-group">
                 <div class="row g-3 mt-1">
                  <div class="col-md-2">
                     <label for="Theoretical_limit_known" class="control-label">Theoretical limit</label>
                     </div>

                 <div class="col-md-2">
                    
                     <select asp-for="Targets.Theoretical_limit_known"  class="form-control form-control-sm custom-select external-comparative-select" id="Theoretical_limit_known">  
                          <option></option>
                         <option value="yes">Available</option>                            
                          <option value="No">Not Available</option>                           
                     </select>
                 </div>
                  <div class="col-md-1">

                          </div>
                          <div class="col-md-1">

                          </div>
                 <div class="col-md-2">
                     <label for="Theoretical_limit_known_Value" class="control-label">Theoretical limit Value </label>
                     </div>

                 <div class="col-md-1">
                    
                    <input asp-for="Targets.Theoretical_limit_known_Value" class="form-control form-control-sm comparative-value" id="Theoretical_limit_known_Value" autocomplete="off">
                 </div>
                 <div class="col-md-1">
                     <label for="Theoretical_limit_known_Details" class="control-label">Details</label>
                     </div>

                 <div class="col-md-2">
                    <input asp-for="Targets.Theoretical_limit_known_Details" class="form-control form-control-sm comparative-details" id="Theoretical_limit_known_Details" autocomplete="off">
                 </div>
                 </div>
                 </div>
                  <div class="external-comparative-group">
                 <div class="row g-3 mt-1">
                  <div class="col-md-2">
                     <label for="Statutory_standard_guidline_known" class="control-label">Statutory standard/guideline</label>
                     </div>

                 <div class="col-md-2">
                    
                     <select asp-for="Targets.Statutory_standard_guidline_known" class="form-control form-control-sm custom-select external-comparative-select" id="Statutory_standard_guidline_known"> 
                          <option></option>
                         <option value="yes">Available</option>                            
                          <option value="No">Not Available</option>                           
                     </select>
                 </div>
                  <div class="col-md-1">

                          </div>
                          <div class="col-md-1">

                          </div>
                 <div class="col-md-2">
                     <label for="Statutory_standard_guidline_known_Value" class="control-label">Statutory standard Value</label>
                     </div>

                 <div class="col-md-1">
                    
                    <input asp-for="Targets.Statutory_standard_guidline_known_Value" class="form-control form-control-sm comparative-value" id="Statutory_standard_guidline_known_Value" autocomplete="off">
                 </div>
                 <div class="col-md-1">
                     <label for="Statutory_standard_guidline_known_Details" class="control-label">Details</label>
                     </div>

                 <div class="col-md-2">
                    <input asp-for="Targets.Statutory_standard_guidline_known_Details" class="form-control form-control-sm comparative-details" id="Statutory_standard_guidline_known_Details" autocomplete="off">
                 </div>
                 </div>
                 </div>
                  <div class="external-comparative-group">
                 <div class="row g-3 mt-1">
                  <div class="col-md-2">
                     <label for="Internal_Benchmark_available" class="control-label">Internal benchmark</label>
                     </div>

                 <div class="col-md-2">
                    
                     <select asp-for="Targets.Internal_Benchmark_available" class="form-control form-control-sm custom-select external-comparative-select" id="Internal_Benchmark_available">
                          <option></option>
                         <option value="yes">Available</option>                            
                          <option value="No">Not Available</option>                           
                     </select>
                 </div>
                  <div class="col-md-1">

                          </div>
                          <div class="col-md-1">

                          </div>
                 <div class="col-md-2">
                     <label for="Internal_Benchmark_available_Value" class="control-label">Internal benchmark Value</label>
                     </div>

                 <div class="col-md-1">
                    
                    <input asp-for="Targets.Internal_Benchmark_available_Value" class="form-control form-control-sm comparative-value" id="Internal_Benchmark_available_Value" autocomplete="off">
                 </div>
                 <div class="col-md-1">
                     <label for="Internal_Benchmark_available_Details" class="control-label">Details</label>
                     </div>

                 <div class="col-md-2">
                    <input asp-for="Targets.Internal_Benchmark_available_Details" class="form-control form-control-sm comparative-details" id="Internal_Benchmark_available_Details" autocomplete="off">
                 </div>
                 </div>
                 </div>
                  <div class="external-comparative-group">
                 <div class="row g-3 mt-1">
                  <div class="col-md-2">
                     <label for="hist1" class="control-label">Historical best</label>
                     </div>

                 <div class="col-md-2">
                    
                     <select asp-for="Targets.Historical_bast_available" class="form-control form-control-sm custom-select external-comparative-select" id="hist1">  
                          <option></option>
                         <option value="yes">Available</option>                            
                          <option value="No">Not Available</option>                           
                     </select>
                 </div>
                  <div class="col-md-1">

                          </div>
                           <div class="col-md-1">

                          </div>
                 <div class="col-md-2">
                     <label for="hist2" class="control-label">Historical Value</label>
                     </div>

                 <div class="col-md-1">
                    
                    <input asp-for="Targets.Historical_bast_available_Value" class="form-control form-control-sm comparative-value" id="hist2" autocomplete="off">
                 </div>
                 <div class="col-md-1">
                     <label for="hist3" class="control-label">Details</label>
                     </div>

                 <div class="col-md-2">
                    <input asp-for="Targets.Historical_bast_available_Details" class="form-control form-control-sm comparative-details" id="hist3" autocomplete="off">
                 </div>
                 </div>

this is my script 

<script>
document.addEventListener('DOMContentLoaded', function () {
    const form = document.getElementById('form');
    const groups = document.querySelectorAll('.external-comparative-group');
    const refNoLinks = document.querySelectorAll('.refNoLink');

    function applyReadOnlyLogic(group) {
        const dropdown = group.querySelector('.external-comparative-select');
        const valueInput = group.querySelector('.comparative-value');
        const detailsInput = group.querySelector('.comparative-details');

        if (!dropdown || !valueInput || !detailsInput) return;

        if (dropdown.value === 'No') {
            valueInput.readOnly = true;
            detailsInput.readOnly = true;

            valueInput.value = '';
            detailsInput.value = '';

            valueInput.classList.remove('is-invalid');
            detailsInput.classList.remove('is-invalid');
        } else {
            valueInput.readOnly = false;
            detailsInput.readOnly = false;
        }
    }


    groups.forEach(group => {
        const dropdown = group.querySelector('.external-comparative-select');
        dropdown.addEventListener('change', function () {
            applyReadOnlyLogic(group);
        });
    });


    refNoLinks.forEach(link => {
        link.addEventListener('click', function (event) {
            event.preventDefault();

        
            groups.forEach(group => {
                applyReadOnlyLogic(group);
            });

        });
    });


    form.addEventListener('submit', function (event) {
        event.preventDefault();
        let isValid = true;
        const elements = this.querySelectorAll('input, select, textarea');

        elements.forEach(function (element) {
            if (['KPIID', 'CreatedBy', 'KPICode', 'ID','value','Periodicity'].includes(element.id)) return;

            if (element.readOnly || element.disabled) {
                element.classList.remove('is-invalid');
                return;
            }

            if (element.value.trim() === '') {
                isValid = false;
                element.classList.add('is-invalid');
            } else {
                element.classList.remove('is-invalid');
            }
        });

        if (isValid) {
            form.submit();
        }
    });
});
</script>
