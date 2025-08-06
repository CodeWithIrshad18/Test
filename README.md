if (match.label === userId && match.distance < 0.35) {
    if (matchMode === "both") {
        const distToBase = faceapi.euclideanDistance(detection.descriptor, baseDescriptor);
        const distToCaptured = faceapi.euclideanDistance(detection.descriptor, capturedDescriptor);

        if (distToBase < 0.35 && distToCaptured < 0.35) {
            onMatchSuccess();
        } else {
            statusText.textContent = "❌ Face does not match with reference images.";
            videoContainer.style.borderColor = "red";
        }
    } else {
        onMatchSuccess();
    }
} else {
    statusText.textContent = "❌ Face does not match reference.";
    videoContainer.style.borderColor = "red";
}

if (matchMode === "both") {
    const distToBase = faceapi.euclideanDistance(detection.descriptor, baseDescriptor);
    const distToCaptured = faceapi.euclideanDistance(detection.descriptor, capturedDescriptor);

    if (distToBase >= 0.35 && distToCaptured >= 0.35) {
        statusText.textContent = "❌ Captured face does not match reference image.";
        videoContainer.style.borderColor = "red";

        onMatchFailure();
        return resetToRetry();
    }
}



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

          
          string? pyrlArea = null;

          

          using (var connection = new SqlConnection(configuration.GetConnectionString("RFID")))
          {
              await connection.OpenAsync();
              string query = "SELECT ema_pyrl_area FROM SAPHRDB.dbo.T_EMPL_ALL WHERE ema_perno = @Pno";
              pyrlArea = await connection.QueryFirstOrDefaultAsync<string>(query, new { Pno });
          }

          

          var record = await context.AppFaceVerificationDetails
              .FirstOrDefaultAsync(x => x.Pno == Pno && x.DateAndTime.Value.Date == today);

          if (record == null)
          {
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
              if (model.Type == "Punch In")
                  record.PunchInSuccess = true;
              else if (model.Type == "Punch Out")
                  record.PunchOutSuccess = true;
          }

          if (model.Type == "Punch In")
          {
              string newCapturedPath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/Images/", $"{Pno}-Captured.jpg");
              SaveBase64ImageToFile(model.ImageData, newCapturedPath);

              StoreData(currentDate, currentTime, null, Pno);
              if (pyrlArea == "JS" || pyrlArea == "ZZ")
                  StoreDataNOPR(currentDate, currentTime, null, Pno);
          }
          else
          {
              StoreData(currentDate, null, currentTime, Pno);
              if (pyrlArea == "JS" || pyrlArea == "ZZ")
                  StoreDataNOPR(currentDate, null, currentTime, Pno);
          }

          await context.SaveChangesAsync();
          return Json(new { success = true, message = "Attendance recorded successfully." });
      }
      catch (Exception ex)
      {
          return Json(new { success = false, message = ex.Message });
      }
  }

this is my js 

fetch("/TSUISLARS/Geo/AttendanceData", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ Type: entryType, ImageData: window.capturedDataURL })
})
    .then(res => res.json())
    .then(data => {
        const now = new Date().toLocaleString();
        if (data.success) {
            statusText.textContent = "";
            Swal.fire("Thank you!", `Attendance Recorded.\nDate & Time: ${now}`, "success")
                .then(() => location.reload());
        } else {
            Swal.fire("Face Verified, But Error!", "Server rejected attendance.", "error")
                .then(() => location.reload());
        }
    })
    .catch(() => {
        Swal.fire("Error!", "Submission failed.", "error");
    });


 public void StoreDataNOPR(string ddMMyy, string tmIn, string tmOut, string Pno)
 {
     using (var connection = new SqlConnection(configuration.GetConnectionString("RFID")))
     {
         connection.Open();

         if (!string.IsNullOrEmpty(tmIn))
         {
             var query = @"
         INSERT INTO PUNCHDATA(EMP_CODE, PUNCHDATE, PUNCHTIME, INOUT,
         POSTMODE) 
         VALUES 
         (@EMP_CODE,
         @PUNCHDATE, 
         @PUNCHTIME,
         @INOUT, 
         @POSTMODE)";

             var parameters = new
             {
                 EMP_CODE = Pno,
                 PUNCHDATE = DateTime.Now.ToString("yyyy-MM-dd"),
                 PUNCHTIME = DateTime.Now.ToString("HH:mm"),
                 INOUT = "I"
             };

             connection.Execute(query, parameters);
         }

         if (!string.IsNullOrEmpty(tmOut))
         {
             var queryOut = @"
         INSERT INTO PUNCHDATA(EMP_CODE, PUNCHDATE, PUNCHTIME, INOUT,
         POSTMODE) 
         VALUES 
         (@EMP_CODE,
         @PUNCHDATE, 
         @PUNCHTIME,
         @INOUT, 
         @POSTMODE)";

             var parameters = new
             {
                 EMP_CODE = Pno,
                 PUNCHDATE = DateTime.Now.ToString("yyyy-MM-dd"),
                 PUNCHTIME = DateTime.Now.ToString("HH:mm"),
                 INOUT = "O"
             };

             connection.Execute(queryOut, parameters);
         }


     }

     }

in place of this error message i want actual error message coming from controller 

Face Verified, But Error!", "Server rejected attendance.", "error"
