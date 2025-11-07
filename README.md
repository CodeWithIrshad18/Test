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

               string storedImagePath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/Images/", $"{Pno}-{Name}.jpg");
               string lastCapturedPath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/Images/", $"{Pno}-Captured.jpg");



               if (!System.IO.File.Exists(storedImagePath) && !System.IO.File.Exists(lastCapturedPath))
               {
                   return Json(new { success = false, message = "No reference image found to verify face!" });
               }

               string tempCapturedPath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/Images/", $"{Pno}-Captured-{DateTime.Now.Ticks}.jpg");

               SaveBase64ImageToFile(model.ImageData, tempCapturedPath);

               bool isFaceMatched = false;

               using (Bitmap tempCaptured = new Bitmap(tempCapturedPath))
               {
                   if (System.IO.File.Exists(storedImagePath))
                   {
                       using (Bitmap stored = new Bitmap(storedImagePath))
                       {
                           isFaceMatched = VerifyFace(tempCaptured, stored);
                       }
                   }

                   if (!isFaceMatched && System.IO.File.Exists(lastCapturedPath))
                   {
                       using (Bitmap lastCaptured = new Bitmap(lastCapturedPath))
                       {
                           isFaceMatched = VerifyFace(tempCaptured, lastCaptured);
                       }
                   }
               }

               System.IO.File.Delete(tempCapturedPath);

               string currentDate = DateTime.Now.ToString("yyyy/MM/dd");
               string currentTime = DateTime.Now.ToString("HH:mm");

               string? pyrlArea = null;


               DateTime today = DateTime.Today;

               using (var connection = new SqlConnection(configuration.GetConnectionString("RFID")))
               {
                   await connection.OpenAsync();
                   string query = "SELECT ema_pyrl_area FROM SAPHRDB.dbo.T_EMPL_ALL WHERE ema_perno = @Pno";
                   pyrlArea = await connection.QueryFirstOrDefaultAsync<string>(query, new { Pno });
               }

               var record = context.AppFaceVerificationDetails
                   .FirstOrDefault(x => x.Pno == Pno && x.DateAndTime.Value.Date == today);

               if (record == null)
               {
                   record = new AppFaceVerificationDetail
                   {
                       Pno = Pno,
                       PunchInFailedCount = 0,
                       PunchOutFailedCount = 0,
                       PunchInSuccess = false,
                       PunchOutSuccess = false
                   };
                   context.AppFaceVerificationDetails.Add(record);
               }

               if (isFaceMatched)
               {
                   if (model.Type == "Punch In")
                   {
                       string newCapturedPath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/Images/", $"{Pno}-Captured.jpg");
                       SaveBase64ImageToFile(model.ImageData, newCapturedPath);

                       StoreData(currentDate, currentTime, null, Pno);
                       if (pyrlArea == "JS" || pyrlArea == "ZZ")
                       StoreDataNOPR(currentDate, currentTime, null, Pno);
                       StoreVersionDetails(currentDate, null, currentTime, Pno); 
                       record.PunchInSuccess = true;
                   }
                   else
                   {
                       StoreData(currentDate, null, currentTime, Pno);
                       if (pyrlArea == "JS" || pyrlArea == "ZZ")
                       StoreDataNOPR(currentDate, null, currentTime, Pno);
                       StoreVersionDetails(currentDate, null, currentTime, Pno);
                       record.PunchOutSuccess = true;
                   }

                   context.SaveChanges();
                   return Json(new { success = true, message = "Attendance recorded successfully." });
               }
               else
               {
                   if (model.Type == "Punch In")
                       record.PunchInFailedCount = (record.PunchInFailedCount ?? 0) + 1;
                   else
                       record.PunchOutFailedCount = (record.PunchOutFailedCount ?? 0) + 1;

                   context.SaveChanges();
                   return Json(new { success = false, message = "Face does not match!" });
               }

           }
           catch (Exception ex)
           {
               return Json(new { success = false, message = ex.Message });
           }
       }



       public void StoreVersionDetails(string Pno, string tmIn, string tmOut)
       {
           using (var connection = new SqlConnection(configuration.GetConnectionString("RFID")))
           {
               connection.Open();


               if (!string.IsNullOrEmpty(tmIn))
               {
                   var query = @"
               INSERT INTO App_VersionDetail (Pno, Version, PunchIN)
               VALUES (@Pno, @Version, @PunchIN)";

                   var parameters = new
                   {
                       Pno,
                       Version = "NEW VERSION",
                       PunchIN = "I"
                   };

                   connection.Execute(query, parameters);
               }


               if (!string.IsNullOrEmpty(tmOut))
               {
                   var query = @"
               INSERT INTO App_VersionDetail (Pno, Version, PunchOUT)
               VALUES (@Pno, @Version, @PunchOUT)";

                   var parameters = new
                   {
                       Pno,
                       Version = "NEW VERSION",
                       PunchOUT = "O"
                   };

                   connection.Execute(query, parameters);
               }
           }
       }
