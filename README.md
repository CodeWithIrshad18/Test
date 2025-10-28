 [HttpGet]
 public async Task<JsonResult> GetTargets(Guid TSID)
 {
     using (var connection = new SqlConnection(GetSAPConnectionString()))
     {
         string query = @"
     select tj.PeriodicityTransactionID,tj.TargetValue from App_KPIMaster_NOPR ts 
     inner join App_TargetSetting_NOPR td
     on ts.ID =td.KPIID
     inner join App_TargetSettingDetails_NOPR tj
     on td.ID = tj.MasterID where td.ID = @TSID";

         var result = await connection.QueryAsync<string>(query, new { TSID = TSID });
         return Json(result);
     }
 }
