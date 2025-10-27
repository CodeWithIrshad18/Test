<style>
  .period-block {
    border: 1px solid #dee2e6;
    border-radius: 6px;
    padding: 12px 15px;
    background-color: #f8f9fa;
    margin-top: 8px;
  }

  .period-block label {
    font-weight: 500;
  }

  .period-block .form-control-sm, 
  .period-block .custom-select {
    height: 30px;
  }
</style>

<div class="row g-3 mt-1">
  <div class="col-md-1">
    <label for="KPIDefination" class="control-label">Defination</label>
  </div>
  <div class="col-md-3">
    <textarea class="form-control form-control-sm" autocomplete="off" id="KPIDefination" name="KPIDefination" style="height:50%;"></textarea>
  </div>

  <div class="col-md-1">
    <label for="UnitCode" class="control-label">UOM</label>  
  </div>
  <div class="col-md-3">
    <input class="form-control form-control-sm" id="UnitCode" autocomplete="off">
  </div>

  <div class="col-md-1">
    <label for="PeriodicityID" class="control-label">Periodicity</label>  
  </div>
  <div class="col-md-3">
    <input class="form-control form-control-sm" id="PeriodicityID" autocomplete="off">
  </div>
</div>

<!-- Period Block -->
<div class="period-block">
  <div class="row g-3">
    <!-- Period -->
    <div class="col-md-1">
      <label for="KPILevel" class="control-label">Period</label>
    </div>
    <div class="col-md-3">
      <select class="form-control form-control-sm custom-select" id="KPILevel">
        <option value="">Select</option>
        <option value="Jan">Jan</option>
        <option value="Feb">Feb</option>
        <option value="Mar">Mar</option>
        <option value="Apr">Apr</option>
      </select>
    </div>

    <!-- Target -->
    <div class="col-md-1">
      <label for="Target" class="control-label">Target</label>
    </div>
    <div class="col-md-3">
      <input type="text" class="form-control form-control-sm" id="Target" autocomplete="off">
    </div>

    <!-- Value -->
    <div class="col-md-1">
      <label for="Value" class="control-label">Value</label>
    </div>
    <div class="col-md-3">
      <input type="text" class="form-control form-control-sm" id="Value" autocomplete="off">
    </div>

    <!-- YTD Value -->
    <div class="col-md-1">
      <label for="YTDValue" class="control-label">YTD Value</label>
    </div>
    <div class="col-md-3">
      <input type="text" class="form-control form-control-sm" id="YTDValue" autocomplete="off">
    </div>
  </div>
</div>


<!-- Existing definition row -->
<div class="row g-3 mt-1">
  <div class="col-md-1">
    <label for="KPIDefination" class="control-label">Definition</label>
  </div>
  <div class="col-md-3">
    <textarea class="form-control form-control-sm" autocomplete="off" id="KPIDefination" name="KPIDefination" style="height:50%;"></textarea>
  </div>
  <div class="col-md-1">
    <label for="UnitCode" class="control-label">UOM</label>  
  </div>
  <div class="col-md-3">
    <input class="form-control form-control-sm" id="UnitCode" autocomplete="off">
  </div>
  <div class="col-md-1">
    <label for="PeriodicityID" class="control-label">Periodicity</label>  
  </div>
  <div class="col-md-3">
    <input class="form-control form-control-sm" id="PeriodicityID" autocomplete="off">
  </div>
</div>

<!-- Period + Target + Value + YTD Value block -->
<div class="card mt-2 shadow-sm border-0" style="background-color:#f8f9fa;">
  <div class="card-body py-3">
    <div class="row g-3 align-items-center">
      <!-- Period -->
      <div class="col-md-1">
        <label for="KPILevel" class="control-label">Period</label>
      </div>
      <div class="col-md-3">
        <select class="form-control form-control-sm custom-select" id="KPILevel">
          <option value="">Select</option>
          <option value="Jan">Jan</option>
          <option value="Feb">Feb</option>
          <option value="Mar">Mar</option>
          <option value="Apr">Apr</option>
        </select>
      </div>

      <!-- Target -->
      <div class="col-md-1">
        <label for="Target" class="control-label">Target</label>
      </div>
      <div class="col-md-3">
        <input type="text" class="form-control form-control-sm" id="Target" autocomplete="off">
      </div>

      <!-- Value -->
      <div class="col-md-1">
        <label for="Value" class="control-label">Value</label>
      </div>
      <div class="col-md-3">
        <input type="text" class="form-control form-control-sm" id="Value" autocomplete="off">
      </div>

      <!-- YTD Value -->
      <div class="col-md-1">
        <label for="YTDValue" class="control-label">YTD Value</label>
      </div>
      <div class="col-md-3">
        <input type="text" class="form-control form-control-sm" id="YTDValue" autocomplete="off">
      </div>
    </div>
  </div>
</div>



this is my frontend side 

                      <div class="row g-3 mt-1">
                      <div class="col-md-1">

                        <label for="KPIDefination" class="control-label">Defination</label>

                        </div>
                         <div class="col-md-3">
                        <textarea class="form-control form-control-sm" autocomplete="off" id="KPIDefination" name="KPIDefination" style="height:50%;"></textarea>
                        </div>
                        <div class="col-md-1">
                        <label for="KPICode" class="control-label">UOM</label>  
                        </div>

                    <div class="col-md-3">
                        <input  class="form-control form-control-sm" id="UnitCode" autocomplete="off">
                    </div>
                    <div class="col-md-1">
                        <label for="KPICode" class="control-label">Periodicity</label>  
                        </div>

                    <div class="col-md-3">
                        <input  class="form-control form-control-sm" id="PeriodicityID" autocomplete="off">
                    </div>

                    </div>

  <div class="row g-3">

  <div class="col-md-1">
    <label for="KPILevel" class="control-label">Period</label>
  </div>
  <div class="col-md-7">
    <select class="form-control form-control-sm custom-select" id="KPILevel">
      <option value="">Select</option>
      <option value="Jan">Jan</option>
      <option value="Feb">Feb</option>
      <option value="Mar">Mar</option>
      <option value="APR">APR</option>
    </select>
  </div>

  <div class="col-md-3">
    <label for="Target" class="control-label">Target</label>
  </div>
  <div class="col-md-1">
    <input type="text" class="form-control form-control-sm" id="Target" autocomplete="off">
  </div>

  <div class="col-md-3 offset-md-8">
    <label for="Value" class="control-label">Value</label>
  </div>
  <div class="col-md-1">
    <input type="text" class="form-control form-control-sm" id="Value" autocomplete="off">
  </div>

  <div class="col-md-3 offset-md-8">
    <label for="YTDValue" class="control-label">YTD Value</label>
  </div>
  <div class="col-md-1">
    <input type="text" class="form-control form-control-sm" id="YTDValue" autocomplete="off">
  </div>
 
</div>


in this i want the period and their values show like in one block to show like related , alignment same but i want to look like some good block or something 
