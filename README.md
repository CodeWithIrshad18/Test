const entryType = EntryTypeInput?.value?.trim();
console.log("Sending Face Match Failure with Type:", entryType);

fetch("/AS/Geo/LogFaceMatchFailure", {
    method: "POST",
    headers: {
        "Content-Type": "application/json"
    },
    body: JSON.stringify({ Type: entryType })
})
.then(res => res.json())
.then(data => {
    console.log("Failure Log Response:", data);
})
.catch(err => console.error("Failed match log error:", err));





fetch("/AS/Geo/LogFaceMatchFailure", {
    method: "POST",
    headers: {
        "Content-Type": "application/json"
    },
    body: JSON.stringify({ Type: "Punch In" })
}).then(res => res.json()).then(console.log);





[HttpPost]
public IActionResult LogFaceMatchFailure([FromBody] AttendanceRequest model)
{
    try
    {
        var userId = HttpContext.Request.Cookies["Session"];
        if (string.IsNullOrEmpty(userId))
            return Json(new { success = false, message = "User session not found!" });

        DateTime today = DateTime.Today;

        var record = context.AppFaceVerificationDetails
            .FirstOrDefault(x => x.Pno == userId && x.DateAndTime.Value.Date == today);

        var type = (model.Type ?? "").Trim().ToLower();

        if (record == null)
        {
            record = new AppFaceVerificationDetail
            {
                Pno = userId,
                DateAndTime = DateTime.Now,
                PunchInFailedCount = (type == "punch in") ? 1 : 0,
                PunchOutFailedCount = (type == "punch out") ? 1 : 0,
                PunchInSuccess = false,
                PunchOutSuccess = false
            };
            context.AppFaceVerificationDetails.Add(record);
        }
        else
        {
            if (type == "punch in")
                record.PunchInFailedCount += 1;
            else if (type == "punch out")
                record.PunchOutFailedCount += 1;
        }

        context.SaveChanges();
        return Json(new { success = true, message = "Face match failure logged.", type });
    }
    catch (Exception ex)
    {
        return Json(new { success = false, message = ex.Message });
    }
}




i have already send you this code 
else {
                statusText.textContent = "Face not matched ❌";
                videoContainer.style.borderColor = "red";

                
                const entryType = EntryTypeInput.value;
                fetch("/AS/Geo/LogFaceMatchFailure", {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json"
                    },
                    body: JSON.stringify({ Type: entryType })
                }).catch(err => console.error("Failed match log error:", err));

                setTimeout(() => {
                    resetBlink();
                    videoContainer.style.borderColor = "gray";
                    detectBlink();
                }, 2000);
            }

[HttpPost]
public IActionResult LogFaceMatchFailure([FromBody] AttendanceRequest model)
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
            record.PunchInFailedCount += 1;
        else if (model.Type == "Punch Out")
            record.PunchOutFailedCount += 1;

        context.SaveChanges();

        return Json(new { success = true, message = "Face match failure logged." });
    }
    catch (Exception ex)
    {
        return Json(new { success = false, message = ex.Message });
    }
}

this is the data that stored 
09D0FF9D-2C53-41DD-A860-8BD9F470BEE6	159445	2025-07-24 11:21:55.430	0	0	0	0

there is no increasing in count for PunchIn and PunchOut
