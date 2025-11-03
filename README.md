if (actionType == "save")
{
    if (!canWrite)
        return RedirectToAction("AccessDenied", "TPR");

    // 🔹 Check if a record already exists for the same KPIID + PeriodTransactionID
    var existingRecord = await context.AppKPIDetails
        .FirstOrDefaultAsync(x => x.KPIID == model.KPIID && x.PeriodTransactionID == model.PeriodTransactionID);

    if (existingRecord == null)
    {
        // 🔹 No record exists — create new
        model.CreatedBy = userId;
        model.CreatedOn = DateTime.Now;

        context.AppKPIDetails.Add(model);
    }
    else
    {
        // 🔹 Record exists — update values
        if (!canModify)
            return RedirectToAction("AccessDenied", "TPR");

        existingRecord.Value = model.Value;
        existingRecord.YTDValue = model.YTDValue;
        existingRecord.UpdatedBy = userId;
        existingRecord.UpdatedOn = DateTime.Now;

        context.AppKPIDetails.Update(existingRecord);
    }

    await context.SaveChangesAsync();
    TempData["Success"] = "Actual KPI saved successfully!";
    return RedirectToAction("ActualKPI");
}

   
   
   
   
   if (actionType == "save")
   {
       if (!canWrite)
       {
           return RedirectToAction("AccessDenied", "TPR");
       }

       var existingRecord1 = await context.AppKPIDetails.FirstOrDefaultAsync(model.KPIID && model.PeriodTransactionID);

       if (model.KPIID == Guid.Empty && model.PeriodTransactionID==Guid.Empty)
       {
           model.CreatedBy = userId;
           model.CreatedOn = DateTime.Now;

           context.AppKPIDetails.Add(model);
       }
       else
       {
           if (!canModify)
           {
               return RedirectToAction("AccessDenied", "TPR");
           }
           var existingRecord = await context.AppKPIDetails.FindAsync(model.ID);
           if (existingRecord != null)
           {
               model.CreatedBy = userId;
               //model.CreatedOn = DateTime.Now;
               existingRecord.Value = model.Value;
               existingRecord.YTDValue = model.YTDValue;
               context.AppKPIDetails.Update(existingRecord);
           }
           else
           {
               return NotFound("Record not found.");
           }
       }
       await context.SaveChangesAsync();
       TempData["Success"] = "Actual KPI Saved successfully!";
       return RedirectToAction("ActualKPI");
   }


 public Guid KPIID { get; set; }
 public Guid? PeriodTransactionID { get; set; }

<input type="hidden" asp-for="KPIID" id="KPIID" />
<input type="hidden" asp-for="PeriodTransactionID" id="PeriodID" name="PeriodTransactionID">
