[HttpPost]
public async Task<IActionResult> AttendanceData([FromBody] AttendanceRequest model)
{
    try
    {
        var userId = HttpContext.Request.Cookies["Session"];
        var userName = HttpContext.Request.Cookies["UserName"];

        if (string.IsNullOrEmpty(userId))
            return Json(new { success = false, message = "User session not found!" });

        string Pno = userId;
        string currentDate = DateTime.Now.ToString("yyyy/MM/dd");
        string currentTime = DateTime.Now.ToString("HH:mm");
        DateTime today = DateTime.Today;

        var record = await context.AppFaceVerificationDetails
            .FirstOrDefaultAsync(x => x.Pno == Pno && x.DateAndTime.Value.Date == today);

        if (record == null)
        {
            // New record
            record = new AppFaceVerificationDetail
            {
                Pno = Pno,
                DateAndTime = DateTime.Now,
                PunchInSuccess = (model.Type == "Punch In"),
                PunchOutSuccess = (model.Type == "Punch Out")
            };
            context.AppFaceVerificationDetails.Add(record);
        }
        else
        {
            // Existing record
            if (model.Type == "Punch In")
                record.PunchInSuccess = true;
            else if (model.Type == "Punch Out")
                record.PunchOutSuccess = true;
        }

        if (model.Type == "Punch In")
        {
            // Save image
            string newCapturedPath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/Images/", $"{Pno}-Captured.jpg");
            SaveBase64ImageToFile(model.ImageData, newCapturedPath);

            StoreData(currentDate, currentTime, null, Pno); // PunchIn
        }
        else
        {
            StoreData(currentDate, null, currentTime, Pno); // PunchOut
        }

        await context.SaveChangesAsync();
        return Json(new { success = true, message = "Attendance recorded successfully." });
    }
    catch (Exception ex)
    {
        return Json(new { success = false, message = ex.Message });
    }
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

        string type = (model.Type ?? "").Trim().ToLower();

        if (record == null)
        {
            // New failure record
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
            // Increment existing failure
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



I have this 2 controller code 
        [HttpPost]
        public async Task<IActionResult> AttendanceData([FromBody] AttendanceRequest model)
        {
            try
            {
                var UserId = HttpContext.Request.Cookies["Session"];
                var UserName = HttpContext.Request.Cookies["UserName"];
                if (string.IsNullOrEmpty(UserId))
                    return Json(new { success = false, message = "User session not found!" });


                string Pno = UserId;
                string Name = UserName;

                bool isFaceMatched = true;


                string currentDate = DateTime.Now.ToString("yyyy/MM/dd");
                string currentTime = DateTime.Now.ToString("HH:mm");

                DateTime today = DateTime.Today;

                var record = await context.AppFaceVerificationDetails.FirstOrDefaultAsync(x => x.Pno == Pno && x.DateAndTime.Value.Date == today);

                if (record == null)
                {
                    record = new AppFaceVerificationDetail
                    {
                        Pno = Pno,
                        DateAndTime = DateTime.Now,
                        PunchInSuccess = true,
                        PunchOutSuccess = true
                    };
                    context.AppFaceVerificationDetails.Add(record);
                }

               
                    if (model.Type == "Punch In")
                    {
                        string newCapturedPath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/Images/", $"{Pno}-Captured.jpg");
                        SaveBase64ImageToFile(model.ImageData, newCapturedPath);

                        StoreData(currentDate, currentTime, null, Pno);
                        
                    }
                    else
                    {
                        StoreData(currentDate, null, currentTime, Pno);
                       
                    }

                   await context.SaveChangesAsync();
                    
                

                return Json(new { success = true, message = "Attendance recorded successfully." });

               

            }
            catch (Exception ex)
            {
                return Json(new { success = false, message = ex.Message });
            }
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


please handle this code 
