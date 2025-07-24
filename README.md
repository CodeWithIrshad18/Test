[HttpPost]
public IActionResult LogFaceVerification([FromBody] AttendanceRequest model)
{
    try
    {
        var userId = HttpContext.Request.Cookies["Session"];
        if (string.IsNullOrEmpty(userId))
            return Json(new { success = false, message = "User session not found!" });

        DateTime today = DateTime.Today;

        var record = context.AppFaceVerificationDetails
            .FirstOrDefault(x => x.Pno == userId && x.DateAndTime.Value.Date == today);

        if (record == null)
        {
            record = new AppFaceVerificationDetail
            {
                Pno = userId,
                DateAndTime = DateTime.Now,
                PunchInFailedCount = 0,
                PunchOutFailedCount = 0,
                PunchInSuccess = false,
                PunchOutSuccess = false
            };
            context.AppFaceVerificationDetails.Add(record);
        }

        if (model.Type == "Punch In")
        {
            if (model.IsSuccess)
                record.PunchInSuccess = true;
            else
                record.PunchInFailedCount += 1;
        }
        else if (model.Type == "Punch Out")
        {
            if (model.IsSuccess)
                record.PunchOutSuccess = true;
            else
                record.PunchOutFailedCount += 1;
        }

        context.SaveChanges();

        return Json(new { success = true, message = "Face verification logged." });
    }
    catch (Exception ex)
    {
        return Json(new { success = false, message = ex.Message });
    }
}

public class AttendanceRequest
{
    public string Type { get; set; }       // "Punch In" or "Punch Out"
    public bool IsSuccess { get; set; }    // true if face matched, false if failed
}
fetch("/AS/Geo/LogFaceVerification", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ Type: entryType, IsSuccess: true })
});
fetch("/AS/Geo/LogFaceVerification", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ Type: entryType, IsSuccess: false })
});



<input type="hidden" id="EntryType" value="@((ViewBag.InOut == "I") ? "Punch In" : "Punch Out")" />

const entryType = document.getElementById("EntryType")?.value || "";

fetch("/AS/Geo/LogFaceMatchFailure", {
    method: "POST",
    headers: {
        "Content-Type": "application/json"
    },
    body: JSON.stringify({ Type: entryType })
});




i think this query will help , this fetches punchIn and PunchOut if the I means PunchIn and O means PunchOut

            string connectionString = GetRFIDConnectionString();

            string query = @"
SELECT COUNT(*) 
FROM T_TRBDGDAT_EARS 
WHERE TRBDGDA_BD_PNO = @Pno 
AND TRBDGDA_BD_DATE = @CurrentDate";


            int punchCount = 0;

            using (var connection = new SqlConnection(connectionString))
            {
                punchCount = connection.QuerySingleOrDefault<int>(query, new { Pno = pno, CurrentDate = currentDate });
            }

            int mod = punchCount % 2;
            ViewBag.InOut = mod == 0 ? "I" : "O";
