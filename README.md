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
    public DateTime? SafetyApprovedOn { get; set; }

    public string SectionalInchargeApprovedBy { get; set; }
    public DateTime? SectionalInchargeApprovedOn { get; set; }

    public DateTime? RequestClosedOn { get; set; }
    public string RequestClosedBy { get; set; }
}

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

            Rs.SafetyHead_CreatedOn AS RequestClosedOn,
            Rs.SafetyHead_Pno AS RequestClosedBy

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

[Authorize]
[HttpGet("barricading-records")]
public async Task<IActionResult> GetBarricadingRecords(
    DateTime fromDate,
    DateTime toDate)
{
    try
    {
        if (fromDate > toDate)
            return BadRequest("FromDate cannot be greater than ToDate.");

        var data = await rSBDataAcess.GetBarricadingEntriesAsync(fromDate, toDate);

        if (!data.Any())
            return NotFound("No barricading records found.");

        return Ok(data);
    }
    catch (Exception ex)
    {
        logger.LogError(ex, "Error fetching barricading records");
        return StatusCode(500, "Internal server error");
    }
}



select Rs.ReqNo as BarricadingRequestNo,Rs.CreatedOn as BarricadingRequestOn,Rs.CreatedBy as BarricadingRequestBy,Dv.Division as Division,Dp.Department as Department,Rs.Start_Date as StartDate,Rs.End_Date as EndDate,
Rs.GISNo as GISApprovalNo,Rs.SafetyHead_Pno as SafetyApprovedBy,Rs.SafetyHead_CreatedOn as SafetyApprovedOn,Rs.SectionHead_Pno as SectionalInchargeApprovedBy,
Rs.SectionHead_CreatedOn as SectionalInchargeApprovedOn,Rs.SafetyHead_CreatedOn as RequestClosedOn,Rs.SafetyHead_Pno as RequestClosedBy
from App_Road_side_barricade_Entry as RS 

inner join App_DivisionMaster as Dv on Dv.ID = RS.Info_DivisionID
inner join App_DepartmentMaster as Dp on Dp.ID = RS.Info_DepartmentID
where Start_Date>='2025-12-01' and End_Date<='2025-12-26' order by Start_Date
