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
