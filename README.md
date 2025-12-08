[HttpGet]
public JsonResult GetDTRDetails(Guid sourceId, Guid feederId, string dtrName)
{
    string query = @"
        SELECT DTRCapacity, NoOfConsumer 
        FROM App_DTRMaster 
        WHERE SourceID = @SourceID
          AND FeederID = @FeederID
          AND DTRName = @DTRName";

    using (var connection = new SqlConnection(GetConnection()))
    {
        var data = connection.QueryFirstOrDefault(query, new {
            SourceID = sourceId,
            FeederID = feederId,
            DTRName = dtrName
        });

        return Json(data);
    }
}
$("#DTRID").change(function () {

    let dtrName = $("#DTRID option:selected").text();   // Get DTR Name text
    let sourceId = $("#SourceID").val();
    let feederId = $("#FeederID").val();

    if (sourceId && feederId && dtrName) {
        $.get("/PS/GetDTRDetails",
            {
                sourceId: sourceId,
                feederId: feederId,
                dtrName: dtrName
            },
            function (data) {
                $("#DTRCapacity").val(data.dtrCapacity);
                $("#NoOfConsumer").val(data.noOfConsumer);
            }
        );
    }
});

        
        
        
        // Get DTR details
        [HttpGet]
        public JsonResult GetDTRDetails(string dtrId)
        {
            string query = @"SELECT DTRCapacity, NoOfConsumer 
                     FROM App_DTRMaster WHERE ID = @ID";

            using (var connection = new SqlConnection(GetConnection()))
            {
                var data = connection.QueryFirstOrDefault(query, new { ID = dtrId });
                return Json(data);
            }
        }

$("#DTRID").change(function () {
    let dtrId = $(this).val();

    if (dtrId) {
        $.get("/PS/GetDTRDetails", { dtrId: dtrId }, function (data) {
            $("#DTRCapacity").val(data.dtrCapacity);
            $("#NoOfConsumer").val(data.noOfConsumer);
        });
    }
});



 SELECT DTRCapacity, NoOfConsumer 
  FROM App_DTRMaster WHERE SourceID ='f6851628-d446-4a7b-b569-31191c1d7d3a' and FeederID='e43da0ea-9c88-4a1a-aafc-09bc234704cc'
   and DTRName ='Testing for DTR'
