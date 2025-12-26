 [Authorize]
 [HttpGet("check-records")]
 public async Task<IActionResult> CheckRecords(string vendorCode, string workOrders)
 {
     try
     {

         var workOrder = workOrders.Split(',', StringSplitOptions.RemoveEmptyEntries)
                                   .Select(w => w.Trim())
                                   .FirstOrDefault();

         if (string.IsNullOrWhiteSpace(workOrder))
             return BadRequest("Invalid work order.");

         var data = await rSBDataAcess.GetRecordsAsync(vendorCode, workOrder);

         if (data == null)
             return NotFound("No exemption found.");

         return Ok(data);
     }
     catch (Exception ex)
     {
         logger.LogError(ex, "Error checking exemptions");
         return StatusCode(500, "Internal server error");
     }
 }



  public async Task<object> GetRecordsAsync(string vendorCode, string workOrder)
  {
      using (var connection = new SqlConnection(_connectionString))
      {
          string sql = @"
      SELECT TOP 1
          VendorCode,
          WorkOrderNo,
          FORMAT(Approved_On,'dd/MM/yyyy') As ApprovalDate,
          Exemption_CC,

          CASE WHEN Wage=1  THEN 'Y' ELSE 'N' END AS WageStatus,
          CASE WHEN PfEsi=1  THEN 'Y' ELSE 'N' END AS PfEsiStatus,
          CASE WHEN Leave=1  THEN 'Y' ELSE 'N' END AS LeaveStatus,
          CASE WHEN Bonus=1  THEN 'Y' ELSE 'N' END AS BonusStatus,
          CASE WHEN LL=1  THEN 'Y' ELSE 'N' END AS LabourLicenseStatus,
          CASE WHEN Grievance=1  THEN 'Y' ELSE 'N' END AS GrievanceStatus,
          CASE WHEN Notice=1  THEN 'Y' ELSE 'N' END AS NoticeStatus,
          CASE WHEN DATEDIFF(DAY, Approved_On, GETDATE()) <= Exemption_CC THEN 'YES' ELSE 'NO' END AS IsExemption

      FROM App_WorkOrder_Exemption
      WHERE VendorCode = @VendorCode
        AND Status = 'Approved'
        AND (
              WorkOrderNo = @WorkOrder
              OR WorkOrderNo LIKE @LikePattern1
              OR WorkOrderNo LIKE @LikePattern2
              OR WorkOrderNo LIKE @LikePattern3
        )
      ORDER BY Approved_On DESC";

          var result = await connection.QueryAsync<RoadSideBarricadingResult>(sql, new
          {
              VendorCode = vendorCode,
              WorkOrder = workOrder,
              LikePattern1 = workOrder + ",%",
              LikePattern2 = "%," + workOrder + ",%",
              LikePattern3 = "%," + workOrder
          });

   
          if (result == null || !result.Any())
          {
              return new { Message = "Data not found" };
          }

          return result;
      }
  }


new query to implement and also pass the FromDate and ToDate in parametrs removes previous logic . give according to new query
