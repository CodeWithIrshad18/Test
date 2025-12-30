      public async Task<IEnumerable<RoadSideBarricadingEntryResult>>
   GetBarricadingEntriesAsync(DateTime fromDate, DateTime toDate)
      {
          using (var connection = new SqlConnection(_connectionString))
          {
              string sql = @"
      SELECT 
          Rs.ReqNo AS BarricadingRequestNo,
          Rs.CreatedOn AS BarricadingRequestOn,
          Rs.CreatedBy AS BarricadingRequestBy,

          Dv.Division AS Division,
          Dp.Department AS Department,

          Rs.Start_Date AS StartDate,
          Rs.End_Date AS EndDate,

          Rs.GISNo AS GISApprovalNo,

          Rs.SafetyHead_Pno AS SafetyApprovedBy,
          Rs.SafetyHead_CreatedOn AS SafetyApprovedOn,

          Rs.SectionHead_Pno AS SectionalInchargeApprovedBy,
          Rs.SectionHead_CreatedOn AS SectionalInchargeApprovedOn,

           Rs.Location_Coordinates as Coordinates,

CASE 
      WHEN Rs.Status = 'Request Closed' 
      THEN Rs.SafetyHead_CreatedOn 
      ELSE NULL 
  END AS RequestClosedOn,

  CASE 
      WHEN Rs.Status = 'Request Closed' 
      THEN Rs.SafetyHead_Pno 
      ELSE NULL 
  END AS RequestClosedBy

      FROM App_Road_side_barricade_Entry RS
      INNER JOIN App_DivisionMaster Dv 
          ON Dv.ID = RS.Info_DivisionID
      INNER JOIN App_DepartmentMaster Dp 
          ON Dp.ID = RS.Info_DepartmentID
      WHERE 
          Rs.Start_Date >= @FromDate
          AND Rs.End_Date <= @ToDate
      ORDER BY Rs.Start_Date";

              return await connection.QueryAsync<RoadSideBarricadingEntryResult>(
                  sql,
                  new
                  {
                      FromDate = fromDate.Date,
                      ToDate = toDate.Date.AddDays(1).AddSeconds(-1)
                  });
          }
      }

 public class RoadSideBarricadingEntryResult
 {
     public string BarricadingRequestNo { get; set; }
     public DateTime BarricadingRequestOn { get; set; }
     public string BarricadingRequestBy { get; set; }

     public string Division { get; set; }
     public string Department { get; set; }

     public DateTime StartDate { get; set; }
     public DateTime EndDate { get; set; }

     public string GISApprovalNo { get; set; }

     public string SafetyApprovedBy { get; set; }
     public DateTime SafetyApprovedOn { get; set; }

     public string SectionalInchargeApprovedBy { get; set; }
     public DateTime SectionalInchargeApprovedOn { get; set; }

     public DateTime RequestClosedOn { get; set; }
     public string RequestClosedBy { get; set; }
     public string Coordinates { get; set; }
 }
