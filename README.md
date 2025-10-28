and this is my dropdown  
<div class="row g-3">
 <div class="col-md-1">
   <label for="Period" class="control-label">Period</label>
 </div>
 <div class="col-md-7">
   <select class="form-control form-control-sm custom-select" id="Period">
     <option value="">Select</option>
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

this is  my query 

 select td.ID,tj.PeriodicityTransactionID,tj.TargetValue from App_KPIMaster_NOPR ts 
 inner join App_TargetSetting_NOPR td
 on ts.ID =td.KPIID
 inner join App_TargetSettingDetails_NOPR tj
 on td.ID = tj.MasterID where td.ID

this is the ID for Where Condition 

data-TSID="@item.TSID"


according to value shows in dropdown as well as in Target textbox 

and this is my data
2016	70%
2025	70%
2017	70%
2012	80%
2018	70%
2021	70%
2024	70%
2020	70%
2009	70%
2023	70%
2022	60%
2010	70%
2008	90%
2011	70%
2026	50%
2013	70%
2019	70%
