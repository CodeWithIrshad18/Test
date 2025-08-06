this is my query to find out ema_pyrl_area 
select ema_pyrl_area from SAPHRDB.dbo.T_EMPL_ALL where ema_perno='151514'


and this is my controller code

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

i want that if the value of ema_pyrl_area is JS and ZZ then i want to store details in new method as well as in StoreData

public void StoreDataNOPR(string ddMMyy, string tmIn, string tmOut, string Pno)
{

}
