
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

                var existingTarget = await context.AppTargets.Where(x => x.KPIID == target.KPIID).FirstOrDefaultAsync();                    

                if (existingTarget != null)
                {
                    if (!canModify) return RedirectToAction("AccessDenied", "TPR");

                    existingTarget.Relevant_comparative_available = target.Relevant_comparative_available;
                    existingTarget.Relevant_comparative_available_Value = target.Relevant_comparative_available_Value;
                    existingTarget.Relevant_comparative_available_Details = target.Relevant_comparative_available_Details;
                    existingTarget.Current_performance_better_than_statutory_standard = target.Current_performance_better_than_statutory_standard;
                    existingTarget.Current_performance_better_than_comparative_Value = target.Current_performance_better_than_comparative_Value;
                    existingTarget.Current_performance_better_than_comparative_Details = target.Current_performance_better_than_comparative_Details;
                    existingTarget.Historical_bast_available = target.Historical_bast_available;
                    existingTarget.Historical_bast_available_Value = target.Historical_bast_available_Value;
                    existingTarget.Historical_bast_available_Details = target.Historical_bast_available_Details;
                    existingTarget.Internal_Benchmark_available = target.Internal_Benchmark_available;
                    existingTarget.Internal_Benchmark_available_Value = target.Internal_Benchmark_available_Value;
                    existingTarget.Internal_Benchmark_available_Details = target.Internal_Benchmark_available_Details;
                    existingTarget.Statutory_standard_guidline_known = target.Statutory_standard_guidline_known;
                    existingTarget.Statutory_standard_guidline_known_Value = target.Statutory_standard_guidline_known_Value;
                    existingTarget.Statutory_standard_guidline_known_Details = target.Statutory_standard_guidline_known_Details;
                    existingTarget.Theoretical_limit_known = target.Theoretical_limit_known;
                    existingTarget.Theoretical_limit_known_Value = target.Theoretical_limit_known_Value;
                    existingTarget.Theoretical_limit_known_Details = target.Theoretical_limit_known_Details;
                    existingTarget.FinYearID = target.FinYearID;

                    context.AppTargets.Update(existingTarget);
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
                        .FirstOrDefaultAsync(x => x.MasterID == existingTarget.ID);

                    if (existingDetail != null)
                    {
                   
                        existingDetail.TargetValue = d.TargetValue; 
                        existingDetail.CreatedOn = DateTime.Now;
                        context.AppTargetDetails.Update(d);

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

error : Cannot insert the value NULL into column 'ID', table 'KPIMSTSUISLDB.dbo.App_TargetSettingDetails_NOPR'; column does not allow nulls. UPDATE fails.
