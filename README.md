var entry = context.Entry(existingTarget);
entry.CurrentValues.SetValues(target);
entry.Property(x => x.ID).IsModified = false;
entry.Property(x => x.KPIID).IsModified = false;




getting this error : 
The property 'AppTarget.ID' is part of a key and so cannot be modified or marked as modified. To change the principal of an existing entity with an identifying foreign key, first delete the dependent and invoke 'SaveChanges', and then associate the dependent with the new principal.


   [HttpPost]
   public async Task<IActionResult> TargetKPI(TargetViewModel model, string actionType)
   {
       var userId = HttpContext.Session.GetString("Session");
       if (string.IsNullOrEmpty(userId))
           return RedirectToAction("AccessDenied", "TPR");

       string formName = "TargetKPI";
       var form = await context.AppFormDetails
           .Where(f => f.FormName == formName)
           .Select(f => f.Id)
           .FirstOrDefaultAsync();

       if (form == default)
           return RedirectToAction("AccessDenied", "TPR");

       bool canModify = await context.AppUserFormPermissions
               .Where(p => p.UserId == userId && p.FormId == form)
               .AnyAsync(p => p.AllowModify == true);
       bool canDelete = await context.AppUserFormPermissions
           .Where(p => p.UserId == userId && p.FormId == form)
           .AnyAsync(p => p.AllowDelete == true);
       bool canWrite = await context.AppUserFormPermissions
           .Where(p => p.UserId == userId && p.FormId == form)
           .AnyAsync(p => p.AllowWrite == true);

       var target = model.Targets;
       var targetDetails = model.TargetDetails ?? new List<AppTargetDetails>();

       if (actionType == "save")
       {
         
           var existingTarget = await context.AppTargets
               .FirstOrDefaultAsync(t => t.KPIID == target.KPIID && t.ID !=null);

           if (existingTarget != null)
           {
         
               if (!canModify) return RedirectToAction("AccessDenied", "TPR");
               context.Entry(existingTarget).CurrentValues.SetValues(target);
           }
           else
           {
           
               if (!canWrite) return RedirectToAction("AccessDenied", "TPR");
               target.ID = Guid.NewGuid();
               target.CreatedBy = userId;
               await context.AppTargets.AddAsync(target);
           }

           await context.SaveChangesAsync(); 

        
           foreach (var d in targetDetails)
           {
              
               var existingDetail = await context.AppTargetDetails
                   .FirstOrDefaultAsync(x => x.MasterID == target.ID && x.PeriodicityTransactionID == d.PeriodicityTransactionID);

               if (existingDetail != null)
               {
                  
                   context.Entry(existingDetail).CurrentValues.SetValues(d);
               }
               else
               {
                  
                   d.ID = Guid.NewGuid();
                   d.MasterID = target.ID;
                   d.CreatedBy = userId;
                   d.CreatedOn = DateTime.Now;
                   await context.AppTargetDetails.AddAsync(d);
               }
           }

           await context.SaveChangesAsync();
           return Json(new { success = true, message = "Data saved successfully." });
       }

       if (actionType == "delete")
       {
           if (!canDelete) return RedirectToAction("AccessDenied", "TPR");

           var targetToDelete = await context.AppTargets.FindAsync(model.Targets.ID);
           if (targetToDelete == null)
               return NotFound();

           var details = context.AppTargetDetails.Where(d => d.MasterID == targetToDelete.ID);
           context.AppTargetDetails.RemoveRange(details);
           context.AppTargets.Remove(targetToDelete);

           await context.SaveChangesAsync();
           return Json(new { success = true, message = "Deleted successfully." });
       }

       return BadRequest("Invalid action.");
   }
