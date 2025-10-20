<div class="external-comparative-group">
  <div class="row g-2 mt-2 align-items-center comparative-row">
    
    <!-- Block 1: Availability -->
    <div class="col-md-4 d-flex align-items-center comparative-block availability-block">
      <label for="Relevant_comparative_available" class="control-label me-2 mb-0">External comparative</label>
      <select class="form-control form-control-sm custom-select external-comparative-select" id="Relevant_comparative_available">
        <option></option>
        <option value="Available">Available</option>
        <option value="Not Available">Not Available</option>
      </select>
    </div>

    <!-- Block 2: Value + Details -->
    <div class="col-md-8 d-flex align-items-center comparative-block">
      <label for="Relevant_comparative_available_Value" class="control-label me-2 mb-0">Value</label>
      <input class="form-control form-control-sm comparative-value me-3" id="Relevant_comparative_available_Value" autocomplete="off">

      <label for="Relevant_comparative_available_Details" class="control-label me-2 mb-0">Details</label>
      <input class="form-control form-control-sm comparative-details" id="Relevant_comparative_available_Details" autocomplete="off">
    </div>

  </div>
</div>


.comparative-row {
  border: 1px solid #dee2e6;
  border-radius: 6px;
  padding: 8px 12px;
  background-color: #fafafa;
}

.comparative-block {
  gap: 8px;
}

.availability-block {
  border-right: 2px solid #ccc;
  padding-right: 12px;
}

.control-label {
  font-weight: 500;
  white-space: nowrap;
}





this is my design side , every row's first column is which decides available and not available. please design according to that
 <div class="external-comparative-group">
  <div class="row g-3 mt-1">
 <div class="col-md-2">
    <label for="Relevant_comparative_available" class="control-label">External comparative</label>
    </div>

<div class="col-md-2">
   
    <select  class="form-control form-control-sm custom-select external-comparative-select" id="Relevant_comparative_available">
        <option></option>
        <option value="Available">Available</option>
        <option value="Not Available">Not Available</option>                           
    </select>
</div>
 <div class="col-md-1">

         </div>
<div class="col-md-2">
    <label for="Relevant_comparative_available_Value" class="control-label">External comparative Value</label>
    </div>
    
<div class="col-md-2">
   
   <input class="form-control form-control-sm comparative-value" id="Relevant_comparative_available_Value" autocomplete="off">
</div>

<div class="col-md-1">
    <label for="Relevant_comparative_available_Details" class="control-label">Details</label>
    </div>

<div class="col-md-2">
   <input class="form-control form-control-sm comparative-details" id="Relevant_comparative_available_Details" autocomplete="off">
</div>
</div>
</div>
                  
 <div class="row g-3 mt-1">
 <div class="col-md-2">
    <label for="Current_performance_better_than_comparative" class="control-label">Current performance</label>
    </div>

<div class="col-md-2">
   
    <select  class="form-control form-control-sm custom-select" id="Current_performance_better_than_comparative">        
        <option value="Available">Available</option>                            
    </select>
</div>
 <div class="col-md-1">

         </div>
<div class="col-md-2">
    <label for="Current_performance_better_than_comparative_Value" class="control-label">Current performance Value</label>
    </div>

<div class="col-md-2">
   
   <input class="form-control form-control-sm" id="Current_performance_better_than_comparative_Value" autocomplete="off">
</div>
<div class="col-md-1">
    <label for="Current_performance_better_than_comparative_Details" class="control-label">Details</label>
    </div>

<div class="col-md-2">
   <input class="form-control form-control-sm" id="Current_performance_better_than_comparative_Details" autocomplete="off">
</div>
</div>
 <div class="external-comparative-group">
<div class="row g-3 mt-1">
 <div class="col-md-2">
    <label for="Theoretical_limit_known" class="control-label">Theoretical limit</label>
    </div>

<div class="col-md-2">
   
    <select  class="form-control form-control-sm custom-select external-comparative-select" id="Theoretical_limit_known">  
         <option></option>
        <option value="Available">Available</option>                            
         <option value="Not Available">Not Available</option>                           
    </select>
</div>
 <div class="col-md-1">

         </div>
<div class="col-md-2">
    <label for="Theoretical_limit_known_Value " class="control-label">Theoretical limit Value </label>
    </div>

<div class="col-md-2">
   
   <input class="form-control form-control-sm comparative-value" id="Theoretical_limit_known_Value" autocomplete="off">
</div>
<div class="col-md-1">
    <label for="Theoretical_limit_known_Details" class="control-label">Details</label>
    </div>

<div class="col-md-2">
   <input class="form-control form-control-sm comparative-details" id="Theoretical_limit_known_Details" autocomplete="off">
</div>
</div>
</div>
 <div class="external-comparative-group">
<div class="row g-3 mt-1">
 <div class="col-md-2">
    <label for="Statutory_standard_guidline_known" class="control-label">Statutory standard/guideline</label>
    </div>

<div class="col-md-2">
   
    <select  class="form-control form-control-sm custom-select external-comparative-select" id="Statutory_standard_guidline_known"> 
         <option></option>
        <option value="Available">Available</option>                            
         <option value="Not Available">Not Available</option>                           
    </select>
</div>
 <div class="col-md-1">

         </div>
<div class="col-md-2">
    <label for="Statutory_standard_guidline_known_Value " class="control-label">Statutory standard Value</label>
    </div>

<div class="col-md-2">
   
   <input class="form-control form-control-sm comparative-value" id="Statutory_standard_guidline_known_Value" autocomplete="off">
</div>
<div class="col-md-1">
    <label for="Statutory_standard_guidline_known_Details" class="control-label">Details</label>
    </div>

<div class="col-md-2">
   <input class="form-control form-control-sm comparative-details" id="Statutory_standard_guidline_known_Details" autocomplete="off">
</div>
</div>
</div>
 <div class="external-comparative-group">
<div class="row g-3 mt-1">
 <div class="col-md-2">
    <label for="Internal_Benchmark_available" class="control-label">Internal benchmark</label>
    </div>

<div class="col-md-2">
   
    <select  class="form-control form-control-sm custom-select external-comparative-select" id="Internal_Benchmark_available">
         <option></option>
        <option value="Available">Available</option>                            
         <option value="Not Available">Not Available</option>                           
    </select>
</div>
 <div class="col-md-1">

         </div>
<div class="col-md-2">
    <label for="Internal_Benchmark_available_Value " class="control-label">Internal benchmark Value</label>
    </div>

<div class="col-md-2">
   
   <input class="form-control form-control-sm comparative-value" id="Internal_Benchmark_available_Value" autocomplete="off">
</div>
<div class="col-md-1">
    <label for="Internal_Benchmark_available_Details" class="control-label">Details</label>
    </div>

<div class="col-md-2">
   <input class="form-control form-control-sm comparative-details" id="Internal_Benchmark_available_Details" autocomplete="off">
</div>
</div>
</div>
 <div class="external-comparative-group">
<div class="row g-3 mt-1">
 <div class="col-md-2">
    <label for="Historical_bast_available" class="control-label">Historical best</label>
    </div>

<div class="col-md-2">
   
    <select  class="form-control form-control-sm custom-select external-comparative-select" id="Historical_bast_available">  
         <option></option>
        <option value="Available">Available</option>                            
         <option value="Not Available">Not Available</option>                           
    </select>
</div>
 <div class="col-md-1">

         </div>
<div class="col-md-2">
    <label for="Historical_bast_available_Value " class="control-label">Historical Value</label>
    </div>

<div class="col-md-2">
   
   <input class="form-control form-control-sm comparative-value" id="Historical_bast_available_Value" autocomplete="off">
</div>
<div class="col-md-1">
    <label for="Historical_bast_available_Details" class="control-label">Details</label>
    </div>

<div class="col-md-2">
   <input class="form-control form-control-sm comparative-details" id="Historical_bast_available_Details" autocomplete="off">
</div>
</div>
