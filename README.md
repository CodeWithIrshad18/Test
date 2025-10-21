this is my controller code , in this i want a new logic that check for KPIID and also Id in the target table , if both are available then update else new records  and as well as for target details if masterId is matched with ID of the target then update 
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
          if (target.ID == Guid.Empty)
          {
              if (!canWrite) return RedirectToAction("AccessDenied", "TPR");
              target.ID = Guid.NewGuid();
              target.CreatedBy = userId;
              await context.AppTargets.AddAsync(target);
          }
          else
          {
              if (!canModify) return RedirectToAction("AccessDenied", "TPR");
              var existing = await context.AppTargets.FindAsync(target.ID);
              if (existing == null) return NotFound();
              context.Entry(existing).CurrentValues.SetValues(target);
          }

         
          var oldDetails = context.AppTargetDetails.Where(d => d.MasterID == target.ID);
          context.AppTargetDetails.RemoveRange(oldDetails);

      
          foreach (var d in targetDetails)
          {
              d.ID = Guid.NewGuid();
              d.MasterID = target.ID;
              d.CreatedBy = userId;
              d.CreatedOn = DateTime.Now;
              await context.AppTargetDetails.AddAsync(d);
          }

          await context.SaveChangesAsync();
          return Json(new { success = true, message = "Data saved successfully." });
      }

      if (actionType == "delete")
      {
          if (!canDelete) return RedirectToAction("AccessDenied", "TPR");
          var targetToDelete = await context.AppTargets.FindAsync(target.ID);
          if (targetToDelete == null) return NotFound();

          var details = context.AppTargetDetails.Where(d => d.MasterID == target.ID);
          context.AppTargetDetails.RemoveRange(details);
          context.AppTargets.Remove(targetToDelete);
          await context.SaveChangesAsync();

          return Json(new { success = true, message = "Deleted successfully." });
      }

      return BadRequest("Invalid action.");
  }
