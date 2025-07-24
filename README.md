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
