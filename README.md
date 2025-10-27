<div class="row g-3 align-items-start mt-2">
  <!-- Left side: Period -->
  <div class="col-md-4 d-flex align-items-center">
    <label for="KPILevel" class="form-label me-2 mb-0" style="width: 60px;">Period</label>
    <select class="form-select form-select-sm w-auto" id="KPILevel">
      <option value="">Select</option>
      <option value="L1">L1</option>
      <option value="L2">L2</option>
      <option value="L3">L3</option>
      <option value="L4">L4</option>
    </select>
  </div>

  <!-- Right side: stacked Target, Value, YTD Value -->
  <div class="col-md-6 offset-md-1">
    <div class="d-flex align-items-center mb-2">
      <label for="Target" class="form-label me-2 mb-0" style="width: 80px;">Target</label>
      <input type="text" class="form-control form-control-sm w-auto flex-grow-1" id="Target" autocomplete="off">
    </div>
    <div class="d-flex align-items-center mb-2">
      <label for="Value" class="form-label me-2 mb-0" style="width: 80px;">Value</label>
      <input type="text" class="form-control form-control-sm w-auto flex-grow-1" id="Value" autocomplete="off">
    </div>
    <div class="d-flex align-items-center">
      <label for="YTDValue" class="form-label me-2 mb-0" style="width: 80px;">YTD Value</label>
      <input type="text" class="form-control form-control-sm w-auto flex-grow-1" id="YTDValue" autocomplete="off">
    </div>
  </div>
</div>





<div class="row g-3 align-items-start mt-2">
  <!-- Left side: Period -->
  <div class="col-md-4">
    <div class="mb-2">
      <label for="KPILevel" class="form-label">Period</label>
      <select class="form-select form-select-sm" id="KPILevel">
        <option value="">Select</option>
        <option value="L1">L1</option>
        <option value="L2">L2</option>
        <option value="L3">L3</option>
        <option value="L4">L4</option>
      </select>
    </div>
  </div>

  <!-- Right side: Target, Value, YTD Value stacked -->
  <div class="col-md-4 offset-md-1">
    <div class="mb-2">
      <label for="Target" class="form-label">Target</label>
      <input type="text" class="form-control form-control-sm" id="Target" autocomplete="off">
    </div>
    <div class="mb-2">
      <label for="Value" class="form-label">Value</label>
      <input type="text" class="form-control form-control-sm" id="Value" autocomplete="off">
    </div>
    <div>
      <label for="YTDValue" class="form-label">YTD Value</label>
      <input type="text" class="form-control form-control-sm" id="YTDValue" autocomplete="off">
    </div>
  </div>
</div>






<div class="row g-3 align-items-center 
  
  mt-2">
  <!-- Period Selection -->
  <div class="col-md-3 d-flex align-items-center">
    <label for="KPILevel" class="form-label me-2 mb-0" style="width: 60px;">Period</label>
    <select class="form-select form-select-sm" id="KPILevel">
      <option value="">Select</option>
      <option value="L1">L1</option>
      <option value="L2">L2</option>
      <option value="L3">L3</option>
      <option value="L4">L4</option>
    </select>
  </div>

  <!-- Target -->
  <div class="col-md-3 d-flex align-items-center">
    <label for="Target" class="form-label me-2 mb-0" style="width: 60px;">Target</label>
    <input type="text" class="form-control form-control-sm" id="Target" autocomplete="off">
  </div>

  <!-- Value -->
  <div class="col-md-3 d-flex align-items-center">
    <label for="Value" class="form-label me-2 mb-0" style="width: 50px;">Value</label>
    <input type="text" class="form-control form-control-sm" id="Value" autocomplete="off">
  </div>

  <!-- YTD Value -->
  <div class="col-md-3 d-flex align-items-center">
    <label for="YTDValue" class="form-label me-2 mb-0" style="width: 75px;">YTD Value</label>
    <input type="text" class="form-control form-control-sm" id="YTDValue" autocomplete="off">
  </div>
</div>




this is my ui 

    <div class="row g-3" style="margin-top:-17px;">
         <div class="col-md-6">

        <div class="col-md-1">
    <label for="KPILevel" class="control-label">Period</label>
    </div>

<div class="col-md-3">
   
    <select class="form-control form-control-sm custom-select" id="KPILevel">
        <option></option>
        <option value="L1">L1</option>
        <option value="L2">L2</option>
        <option value="L3">L3</option>
        <option value="L4">L4</option>
    </select>
</div>
</div>

 <div class="col-md-6">

 <div class="col-md-1">
    <label for="KPICode" class="control-label">Target</label>  
    </div>

<div class="col-md-3">
    <input  class="form-control form-control-sm" id="KPICode" autocomplete="off">
</div>
 <div class="col-md-1">
    <label for="KPICode" class="control-label">Value</label>  
    </div>

<div class="col-md-3">
    <input  class="form-control form-control-sm" id="KPICode" autocomplete="off">
</div>

 <div class="col-md-1">
    <label for="KPICode" class="control-label">YTD Value</label>  
    </div>

<div class="col-md-3">
    <input  class="form-control form-control-sm" id="KPICode" autocomplete="off">
</div>
</div>


       </div>


in this I want like for user select period and values are showing in three boxes , please fix design according to this logic 
