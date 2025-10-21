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
        .AnyAsync(p => p.UserId == userId && p.FormId == form && p.AllowModify);
    bool canDelete = await context.AppUserFormPermissions
        .AnyAsync(p => p.UserId == userId && p.FormId == form && p.AllowDelete);
    bool canWrite = await context.AppUserFormPermissions
        .AnyAsync(p => p.UserId == userId && p.FormId == form && p.AllowWrite);

    var target = model.Targets;
    var targetDetails = model.TargetDetails ?? new List<AppTargetDetails>();

    if (actionType == "save")
    {
        // ✅ Step 1: Check if record exists by BOTH KPIID and ID
        var existingTarget = await context.AppTargets
            .FirstOrDefaultAsync(t => t.KPIID == target.KPIID && t.ID == target.ID);

        if (existingTarget != null)
        {
            // ✅ Both ID and KPIID match → UPDATE
            if (!canModify) return RedirectToAction("AccessDenied", "TPR");
            context.Entry(existingTarget).CurrentValues.SetValues(target);
        }
        else
        {
            // ✅ Not found → INSERT
            if (!canWrite) return RedirectToAction("AccessDenied", "TPR");
            target.ID = Guid.NewGuid();
            target.CreatedBy = userId;
            target.CreatedOn = DateTime.Now;
            await context.AppTargets.AddAsync(target);
        }

        await context.SaveChangesAsync(); // save target changes first

        // ✅ Step 2: Handle TargetDetails
        foreach (var d in targetDetails)
        {
            // Check if a detail exists for this MasterID and Periodicity (or any unique field)
            var existingDetail = await context.AppTargetDetails
                .FirstOrDefaultAsync(x => x.MasterID == target.ID && x.PeriodicityTransactionID == d.PeriodicityTransactionID);

            if (existingDetail != null)
            {
                // ✅ If MasterID matches → update
                context.Entry(existingDetail).CurrentValues.SetValues(d);
            }
            else
            {
                // ✅ Else insert
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
        .AnyAsync(p => p.UserId == userId && p.FormId == form && p.AllowModify);
    bool canDelete = await context.AppUserFormPermissions
        .AnyAsync(p => p.UserId == userId && p.FormId == form && p.AllowDelete);
    bool canWrite = await context.AppUserFormPermissions
        .AnyAsync(p => p.UserId == userId && p.FormId == form && p.AllowWrite);

    var target = model.Targets;
    var targetDetails = model.TargetDetails ?? new List<AppTargetDetails>();

    if (actionType == "save")
    {
        // ✅ STEP 1: Check if Target exists (by KPIID + ID)
        var existingTarget = await context.AppTargets
            .FirstOrDefaultAsync(t => t.KPIID == target.KPIID && t.ID == target.ID);

        if (existingTarget != null)
        {
            // ✅ Update existing
            if (!canModify) return RedirectToAction("AccessDenied", "TPR");
            context.Entry(existingTarget).CurrentValues.SetValues(target);
            existingTarget.CreatedBy ??= userId; // preserve CreatedBy if null
            existingTarget.CreatedOn = existingTarget.CreatedOn == default ? DateTime.Now : existingTarget.CreatedOn;
        }
        else
        {
            // ✅ Insert new
            if (!canWrite) return RedirectToAction("AccessDenied", "TPR");
            target.ID = Guid.NewGuid();
            target.CreatedBy = userId;
            target.CreatedOn = DateTime.Now;
            await context.AppTargets.AddAsync(target);
        }

        await context.SaveChangesAsync(); // save to get ID for target.ID if new

        // ✅ STEP 2: Handle TargetDetails (update or insert)
        foreach (var d in targetDetails)
        {
            // match details by MasterID + unique field (if any)
            var existingDetail = await context.AppTargetDetails
                .FirstOrDefaultAsync(x => x.MasterID == target.ID && x.PeriodicityTransactionID == d.PeriodicityTransactionID);

            if (existingDetail != null)
            {
                // update existing detail
                context.Entry(existingDetail).CurrentValues.SetValues(d);
                existingDetail.MasterID = target.ID;
                existingDetail.CreatedBy ??= userId;
            }
            else
            {
                // insert new detail
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

    // ✅ STEP 3: Delete
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
