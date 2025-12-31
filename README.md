 [HttpPost]
 [ValidateAntiForgeryToken]
 [PreventFlood(3)]
 public async Task<IActionResult> LocationMaster([FromBody] LocationMasterViewModel model, Guid? Id)
 {


     var UserId = HttpContext.Request.Cookies["Session"];
     if (model == null || model.appLocations == null || !model.appLocations.Any())
     {
         return BadRequest("No location data received.");
     }



     if (string.IsNullOrEmpty(model.actionType))
     {
         return BadRequest("No action specified.");
     }

     if (model.actionType == "Submit")
     {
         if (!ModelState.IsValid)
         {
             foreach (var state in ModelState)
             {
                 foreach (var error in state.Value.Errors)
                 {
                     Console.WriteLine($"Key:{state.Key}, Error:{error.ErrorMessage}");
                 }
             }
         }

         foreach (var appLocation in model.appLocations)
         {

             if (ContainsHtml(appLocation.WorkSite))
             {
                 ModelState.AddModelError("Name", "HTML tags are not allowed.");
                 return View(model);
             }

             var existingLocation = await context.AppLocationMasters.FindAsync(appLocation.Id);



             appLocation.CreatedBy = UserId;
             if (existingLocation != null)
             {


                 context.Entry(existingLocation).CurrentValues.SetValues(appLocation);

             }
             else
             {

                 await context.AppLocationMasters.AddAsync(appLocation);
             }
         }


         await context.SaveChangesAsync();


         TempData["msg"] = "Location Saved Successfully!";
         return RedirectToAction("LocationMaster");
     }
     else if (model.actionType == "Delete")
     {
         foreach (var appLocation in model.appLocations)
         {

             var existingLocation = await context.AppLocationMasters.FindAsync(appLocation.Id);
             if (existingLocation != null)
             {

                 context.AppLocationMasters.Remove(existingLocation);
             }
         }


         await context.SaveChangesAsync();


         TempData["Dltmsg"] = "Location Deleted Successfully!";
         return RedirectToAction("LocationMaster");
     }

     return View(model);
 }

Form Submission Flooding:
It was observed that uncountable number of form submissions were possible in the mentioned URL.


The consequences of unrestricted Form Submissions can result in
traffic flooding allowing an attacker to target the application system for DoS (Denial of Service) or DDoS(Distributed Denial of
Service) attacks. A DoS attack tries to make a web resource unavailable to its users by flooding the target URL with more requests than the server can handle. This can lead to unavailability of feedback service. The users will not be able to submit feedback. leading to Reputational damage to Tsuisl.
