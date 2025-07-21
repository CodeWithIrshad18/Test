this is my controller code 

 [HttpPost]
 public IActionResult AttendanceData([FromBody] AttendanceRequest model)
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

        
             //DateTime today = DateTime.Today;

             //var record = context.AppFaceVerificationDetails
             //    .FirstOrDefault(x => x.Pno == Pno && x.DateAndTime.Value.Date == today);

             //if (record == null)
             //{
             //    record = new AppFaceVerificationDetail
             //    {
             //        Pno = Pno,
             //        PunchInFailedCount = 0,
             //        PunchOutFailedCount = 0,
             //        PunchInSuccess = false,
             //        PunchOutSuccess = false
             //    };
             //    context.AppFaceVerificationDetails.Add(record);
             //}
        

         if (isFaceMatched)
         {
             if (model.Type == "Punch In")
             {
                 string newCapturedPath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/Images/", $"{Pno}-Captured.jpg");
                 SaveBase64ImageToFile(model.ImageData, newCapturedPath);

                 StoreData(currentDate, currentTime, null, Pno);
                 //record.PunchInSuccess = true;
             }
             else
             {
                 StoreData(currentDate, null, currentTime, Pno);
                 //record.PunchOutSuccess = true;
             }

             context.SaveChanges();
             return Json(new { success = true, message = "Attendance recorded successfully." });
         }

         

         return Json(new { success = false, message = "Face verification failed." }); 
     
     }
     catch (Exception ex)
     {
         return Json(new { success = false, message = ex.Message });
     }
 }


i this the comment part of the code is to store failecount of punchIn and PunchOut and it increases +1 if the failed happens and if it success it goes true and store failed count in AppFaceVerificationDetails 

and this is my js for failed and success matching 
if (match.label === userId && match.distance < 0.35) {
    statusText.textContent = `${userName}, Face matched ✅`;
  

    setTimeout(() => {
        const captureCanvas = document.createElement("canvas");
        captureCanvas.width = video.videoWidth;
        captureCanvas.height = video.videoHeight;

        const ctx = captureCanvas.getContext("2d");
        ctx.translate(captureCanvas.width, 0);
        ctx.scale(-1, 1);
        ctx.drawImage(video, 0, 0, captureCanvas.width, captureCanvas.height);

        imageCaptured = captureCanvas.toDataURL("image/jpeg");
        capturedImage.src = imageCaptured;
        capturedImage.style.display = "block";
        video.style.display = "none";

        if (punchInButton) punchInButton.style.display = "inline-block";
        if (punchOutButton) punchOutButton.style.display = "inline-block";
    }, 100);
} else {
    statusText.textContent = "Face not matched ❌";
    videoContainer.style.borderColor = "red";
    setTimeout(() => {
        resetBlink();
        videoContainer.style.borderColor = "gray";
        detectBlink();
    }, 2000);
}

please provide me to store these counts of failed for PunchIn and PunchOut
