[HttpPost]
public async Task<IActionResult> PeriodicityMasterSave(AppPeriodicityMaster model)
{
    var user = HttpContext.Session.GetString("Session");

    DateTime? ParseDate(string value)
    {
        if (string.IsNullOrWhiteSpace(value)) return null;
        return DateTime.TryParse(value, out var dt) ? dt : (DateTime?)null;
    }

    try
    {
        string code = model.PeriodicityCode;

        // ==================== MONTHLY ====================
        if (code == "Monthly")
        {
            var record = await context.AppPeriodicityMasters
                .FirstOrDefaultAsync(x => x.ID == model.ID);

            if (record == null)
            {
                record = new AppPeriodicityMaster
                {
                    ID = Guid.NewGuid(),
                    PeriodicityCode = "Monthly",
                    Category = "Monthly",
                    CreatedBy = user,
                    CreatedOn = DateTime.Now
                };

                context.AppPeriodicityMasters.Add(record);
            }

            record.KPISPOC = ParseDate(Request.Form["KpiSPOC"]);
            record.ImmediateSuperior = ParseDate(Request.Form["ImmediateSuperior"]);
            record.HOD = ParseDate(Request.Form["HOD"]);

            record.ModifiedBy = user;
            record.ModifiedOn = DateTime.Now;
        }

        // ==================== YEARLY ====================
        if (code == "Yearly")
        {
            var record = await context.AppPeriodicityMasters
                .FirstOrDefaultAsync(x => x.ID == model.ID);

            if (record == null)
            {
                record = new AppPeriodicityMaster
                {
                    ID = Guid.NewGuid(),
                    PeriodicityCode = "Yearly",
                    Category = "Yearly",
                    CreatedBy = user,
                    CreatedOn = DateTime.Now
                };

                context.AppPeriodicityMasters.Add(record);
            }

            record.KPISPOC = ParseDate(Request.Form["KpiSPOC"]);
            record.ImmediateSuperior = ParseDate(Request.Form["ImmediateSuperior"]);
            record.HOD = ParseDate(Request.Form["HOD"]);

            record.ModifiedBy = user;
            record.ModifiedOn = DateTime.Now;
        }

        // ==================== QUARTERLY ====================
        if (code == "Quarterly")
        {
            var quarters = new Dictionary<string, string>
            {
                { "Q1", "Apr - Jun (Q1 - 1st Qtr)" },
                { "Q2", "Jul - Sep (Q2 - 2nd Qtr)" },
                { "Q3", "Oct - Dec (Q3 - 3rd Qtr)" },
                { "Q4", "Jan - Mar (Q4 - 4th Qtr)" }
            };

            foreach (var q in quarters)
            {
                var existing = await context.AppPeriodicityMasters
                    .FirstOrDefaultAsync(x =>
                        x.PeriodicityCode == "Quarterly" &&
                        x.Category == q.Value);

                if (existing == null)
                {
                    existing = new AppPeriodicityMaster
                    {
                        ID = Guid.NewGuid(),
                        PeriodicityCode = "Quarterly",
                        Category = q.Value,
                        CreatedBy = user,
                        CreatedOn = DateTime.Now
                    };

                    context.AppPeriodicityMasters.Add(existing);
                }

                existing.KPISPOC = ParseDate(Request.Form[$"{q.Key}_KPISPOC"]);
                existing.ImmediateSuperior = ParseDate(Request.Form[$"{q.Key}_ImmediateSuperior"]);
                existing.HOD = ParseDate(Request.Form[$"{q.Key}_HOD"]);

                existing.ModifiedBy = user;
                existing.ModifiedOn = DateTime.Now;
            }
        }

        await context.SaveChangesAsync();
        TempData["Success"] = "Periodicity saved successfully";
        return RedirectToAction("PeriodicityMaster");
    }
    catch (Exception ex)
    {
        TempData["Error"] = ex.Message;
        return RedirectToAction("PeriodicityMaster");
    }
}





[HttpPost]
public async Task<IActionResult> PeriodicityMasterSave(AppPeriodicityMaster model)
{
    var user = HttpContext.Session.GetString("Session");

    DateTime? ParseDate(string value)
    {
        if (string.IsNullOrWhiteSpace(value)) return null;
        return DateTime.TryParse(value, out var dt) ? dt : (DateTime?)null;
    }

    try
    {
        string code = model.PeriodicityCode;

        // ==================== MONTHLY ====================
        if (code == "Monthly")
        {
            var record = await context.AppPeriodicityMasters
                .FirstOrDefaultAsync(x => x.ID == model.ID);

            if (record == null)
            {
                record = new AppPeriodicityMaster
                {
                    ID = Guid.NewGuid(),
                    PeriodicityCode = "Monthly",
                    Category = "Monthly",
                    CreatedBy = user,
                    CreatedOn = DateTime.Now
                };

                context.AppPeriodicityMasters.Add(record);
            }

            record.KPISPOC = ParseDate(Request.Form["KpiSPOC"]);
            record.ImmediateSuperior = ParseDate(Request.Form["ImmediateSuperior"]);
            record.HOD = ParseDate(Request.Form["HOD"]);

            record.ModifiedBy = user;
            record.ModifiedOn = DateTime.Now;
        }

        // ==================== YEARLY ====================
        if (code == "Yearly")
        {
            var record = await context.AppPeriodicityMasters
                .FirstOrDefaultAsync(x => x.ID == model.ID);

            if (record == null)
            {
                record = new AppPeriodicityMaster
                {
                    ID = Guid.NewGuid(),
                    PeriodicityCode = "Yearly",
                    Category = "Yearly",
                    CreatedBy = user,
                    CreatedOn = DateTime.Now
                };

                context.AppPeriodicityMasters.Add(record);
            }

            record.KPISPOC = ParseDate(Request.Form["KpiSPOC"]);
            record.ImmediateSuperior = ParseDate(Request.Form["ImmediateSuperior"]);
            record.HOD = ParseDate(Request.Form["HOD"]);

            record.ModifiedBy = user;
            record.ModifiedOn = DateTime.Now;
        }

        // ==================== QUARTERLY ====================
        if (code == "Quarterly")
        {
            var quarters = new Dictionary<string, string>
            {
                { "Q1", "Apr - Jun (Q1 - 1st Qtr)" },
                { "Q2", "Jul - Sep (Q2 - 2nd Qtr)" },
                { "Q3", "Oct - Dec (Q3 - 3rd Qtr)" },
                { "Q4", "Jan - Mar (Q4 - 4th Qtr)" }
            };

            foreach (var q in quarters)
            {
                var existing = await context.AppPeriodicityMasters
                    .FirstOrDefaultAsync(x =>
                        x.PeriodicityCode == "Quarterly" &&
                        x.Category == q.Value);

                if (existing == null)
                {
                    existing = new AppPeriodicityMaster
                    {
                        ID = Guid.NewGuid(),
                        PeriodicityCode = "Quarterly",
                        Category = q.Value,
                        CreatedBy = user,
                        CreatedOn = DateTime.Now
                    };

                    context.AppPeriodicityMasters.Add(existing);
                }

                existing.KPISPOC = ParseDate(Request.Form[$"{q.Key}_KPISPOC"]);
                existing.ImmediateSuperior = ParseDate(Request.Form[$"{q.Key}_ImmediateSuperior"]);
                existing.HOD = ParseDate(Request.Form[$"{q.Key}_HOD"]);

                existing.ModifiedBy = user;
                existing.ModifiedOn = DateTime.Now;
            }
        }

        await context.SaveChangesAsync();
        TempData["Success"] = "Periodicity saved successfully";
        return RedirectToAction("PeriodicityMaster");
    }
    catch (Exception ex)
    {
        TempData["Error"] = ex.Message;
        return RedirectToAction("PeriodicityMaster");
    }
}





document.getElementById("PeriodicityId").value = this.dataset.id;



if (model.PeriodicityCode == "Quarterly")
{
    if (!canModify)
        return RedirectToAction("AccessDenied", "TPR");

    var existing = await context.AppPeriodicityMasters
                    .FirstOrDefaultAsync(x => x.ID == model.ID);

    if (existing == null)
        return NotFound("Quarter record not found.");

    string category = existing.Category;

    if (category.Contains("Q1"))
    {
        existing.KPISPOC = string.IsNullOrEmpty(Q1_KPISPOC) ? null : DateTime.Parse(Q1_KPISPOC);
        existing.ImmediateSuperior = string.IsNullOrEmpty(Q1_Superior) ? null : DateTime.Parse(Q1_Superior);
        existing.HOD = string.IsNullOrEmpty(Q1_HOD) ? null : DateTime.Parse(Q1_HOD);
    }
    else if (category.Contains("Q2"))
    {
        existing.KPISPOC = string.IsNullOrEmpty(Q2_KPISPOC) ? null : DateTime.Parse(Q2_KPISPOC);
        existing.ImmediateSuperior = string.IsNullOrEmpty(Q2_Superior) ? null : DateTime.Parse(Q2_Superior);
        existing.HOD = string.IsNullOrEmpty(Q2_HOD) ? null : DateTime.Parse(Q2_HOD);
    }
    else if (category.Contains("Q3"))
    {
        existing.KPISPOC = string.IsNullOrEmpty(Q3_KPISPOC) ? null : DateTime.Parse(Q3_KPISPOC);
        existing.ImmediateSuperior = string.IsNullOrEmpty(Q3_Superior) ? null : DateTime.Parse(Q3_Superior);
        existing.HOD = string.IsNullOrEmpty(Q3_HOD) ? null : DateTime.Parse(Q3_HOD);
    }
    else if (category.Contains("Q4"))
    {
        existing.KPISPOC = string.IsNullOrEmpty(Q4_KPISPOC) ? null : DateTime.Parse(Q4_KPISPOC);
        existing.ImmediateSuperior = string.IsNullOrEmpty(Q4_Superior) ? null : DateTime.Parse(Q4_Superior);
        existing.HOD = string.IsNullOrEmpty(Q4_HOD) ? null : DateTime.Parse(Q4_HOD);
    }

    existing.CreatedBy = user;
    existing.CreatedOn = DateTime.Now;

    context.AppPeriodicityMasters.Update(existing);
    await context.SaveChangesAsync();

    TempData["Success"] = "Quarter updated successfully!";
    return RedirectToAction("PeriodicityMaster");
}

        
        
        
        
        [HttpPost]

        public async Task<IActionResult> PeriodicityMaster(AppPeriodicityMaster model, string actionType)
        {
            var user = HttpContext.Session.GetString("Session");

            var userIdString = HttpContext.Session.GetString("Session");
            if (string.IsNullOrEmpty(userIdString))
            {
                return RedirectToAction("AccessDenied", "TPR");
            }

            string formName = "PeriodicityMaster";
            var form = await context.AppFormDetails
                .Where(f => f.FormName == formName)
                .Select(f => f.Id)
                .FirstOrDefaultAsync();

            if (form == default)
            {
                return RedirectToAction("AccessDenied", "TPR");
            }

            bool canModify = await context.AppUserFormPermissions
                .Where(p => p.UserId == userIdString && p.FormId == form)
                .AnyAsync(p => p.AllowModify == true);

            bool canDelete = await context.AppUserFormPermissions
                .Where(p => p.UserId == userIdString && p.FormId == form)
                .AnyAsync(p => p.AllowDelete == true);
            bool canWrite = await context.AppUserFormPermissions
                .Where(p => p.UserId == userIdString && p.FormId == form)
                .AnyAsync(p => p.AllowWrite == true);

            if (actionType == "save")
            {
                if (!canWrite)
                {
                    return RedirectToAction("AccessDenied", "TPR");
                }

                var Q1_KPISPOC = Request.Form["Q1_KPISPOC"].ToString();
                var Q1_Superior = Request.Form["Q1_ImmediateSuperior"].ToString();
                var Q1_HOD = Request.Form["Q1_HOD"].ToString();

                var Q2_KPISPOC = Request.Form["Q2_KPISPOC"].ToString();
                var Q2_Superior = Request.Form["Q2_ImmediateSuperior"].ToString();
                var Q2_HOD = Request.Form["Q2_HOD"].ToString();

                var Q3_KPISPOC = Request.Form["Q3_KPISPOC"].ToString();
                var Q3_Superior = Request.Form["Q3_ImmediateSuperior"].ToString();
                var Q3_HOD = Request.Form["Q3_HOD"].ToString();

                var Q4_KPISPOC = Request.Form["Q4_KPISPOC"].ToString();
                var Q4_Superior = Request.Form["Q4_ImmediateSuperior"].ToString();
                var Q4_HOD = Request.Form["Q4_HOD"].ToString();


                if (model.PeriodicityCode == "Monthly" || model.PeriodicityCode == "Yearly")
                {
                    model.CreatedBy = user;
                    model.PeriodicityName = model.PeriodicityCode;
                    model.Category = model.PeriodicityCode;
                    model.CreatedOn = DateTime.Now;

                    context.AppPeriodicityMasters.Add(model);
                    await context.SaveChangesAsync();
                    TempData["Success"] = "Periodicity Saved successfully!";
                    return RedirectToAction("PeriodicityMaster");
                }


                if (model.PeriodicityCode == "Quarterly")
                {
                    List<AppPeriodicityMaster> quarters = new List<AppPeriodicityMaster>();

                    quarters.Add(new AppPeriodicityMaster
                    {
                        ID = Guid.NewGuid(),
                        PeriodicityCode = "Quarterly",
                        PeriodicityName = "Quarterly",
                        KPISPOC = string.IsNullOrEmpty(Q1_KPISPOC) ? null : DateTime.Parse(Q1_KPISPOC),
                        ImmediateSuperior = string.IsNullOrEmpty(Q1_Superior) ? null : DateTime.Parse(Q1_Superior),
                        HOD = string.IsNullOrEmpty(Q1_HOD) ? null : DateTime.Parse(Q1_HOD),
                        CreatedBy = user,
                        Category = "Apr - Jun (Q1 - 1st Qtr)",
                        CreatedOn = DateTime.Now
                    });

                    quarters.Add(new AppPeriodicityMaster
                    {
                        ID = Guid.NewGuid(),
                        PeriodicityCode = "Quarterly",
                        PeriodicityName = "Quarterly",
                        KPISPOC = string.IsNullOrEmpty(Q2_KPISPOC) ? null : DateTime.Parse(Q2_KPISPOC),
                        ImmediateSuperior = string.IsNullOrEmpty(Q2_Superior) ? null : DateTime.Parse(Q2_Superior),
                        HOD = string.IsNullOrEmpty(Q2_HOD) ? null : DateTime.Parse(Q2_HOD),
                        CreatedBy = user,
                        Category = "Jul - Sep (Q2 - 2nd Qtr)",
                        CreatedOn = DateTime.Now
                    });

                    quarters.Add(new AppPeriodicityMaster
                    {
                        ID = Guid.NewGuid(),
                        PeriodicityCode = "Quarterly",
                        PeriodicityName = "Quarterly",
                        KPISPOC = string.IsNullOrEmpty(Q3_KPISPOC) ? null : DateTime.Parse(Q3_KPISPOC),
                        ImmediateSuperior = string.IsNullOrEmpty(Q3_Superior) ? null : DateTime.Parse(Q3_Superior),
                        HOD = string.IsNullOrEmpty(Q3_HOD) ? null : DateTime.Parse(Q3_HOD),
                        CreatedBy = user,
                        Category = "Oct - Dec (Q3 - 3rd Qtr)",
                        CreatedOn = DateTime.Now
                    });

                    quarters.Add(new AppPeriodicityMaster
                    {
                        ID = Guid.NewGuid(),
                        PeriodicityCode = "Quarterly",
                        PeriodicityName = "Quarterly",
                        KPISPOC = string.IsNullOrEmpty(Q4_KPISPOC) ? null : DateTime.Parse(Q4_KPISPOC),
                        ImmediateSuperior = string.IsNullOrEmpty(Q4_Superior) ? null : DateTime.Parse(Q4_Superior),
                        HOD = string.IsNullOrEmpty(Q4_HOD) ? null : DateTime.Parse(Q4_HOD),
                        CreatedBy = user,
                        Category = "Jan - Mar (Q4 - 4th Qtr)",
                        CreatedOn = DateTime.Now
                    });

                    context.AppPeriodicityMasters.AddRange(quarters);
                    await context.SaveChangesAsync();

                    TempData["Success"] = "Quarterly Periodicity saved successfully!";
                        return RedirectToAction("PeriodicityMaster");
                }

                else
                {
                    if (!canModify)
                    {
                        return RedirectToAction("AccessDenied", "TPR");
                    }
                    var existingRecord = await context.AppPeriodicityMasters.FindAsync(model.ID);
                    if (existingRecord != null)
                    {
                        existingRecord.PeriodicityCode = model.PeriodicityCode;
                        existingRecord.PeriodicityName = model.PeriodicityCode;                        
                        existingRecord.CreatedBy = user;
                        context.AppPeriodicityMasters.Update(existingRecord);
                    }
                    else
                    {
                        return NotFound("Record not found.");
                    }
                }
                await context.SaveChangesAsync();
                TempData["Success"] = "Periodicity Saved successfully!";
                return RedirectToAction("PeriodicityMaster");
            }


<a href="#" class="refNoLink" data-id="f1898ce6-e7fa-4726-9e1b-680ba4f981f9" data-periodicitycode="Quarterly" data-periodicityname="Quarterly" data-category="Oct - Dec (Q3 - 3rd Qtr)" data-kpispoc="18-11-2025 12:04:00" data-immediatesuperior="25-11-2025 12:04:00" data-hod="22-11-2025 12:04:00" data-createdby="151514">
   Quarterly
</a>
            else if (actionType == "delete")
            {
                if (!canDelete)
                {
                    return RedirectToAction("AccessDenied", "TPR");
                }
                var periodicity = await context.AppPeriodicityMasters.FindAsync(model.ID);
                if (periodicity == null)
                {
                    return NotFound("Record not found.");
                }

                context.AppPeriodicityMasters.Remove(periodicity);
                await context.SaveChangesAsync();
                TempData["Delete"] = "Periodicity deleted successfully!";
                return RedirectToAction("PeriodicityMaster");
            }

            return BadRequest("Invalid action.");
        }


document.querySelectorAll(".refNoLink").forEach(link => {
    link.addEventListener("click", function (e) {
        e.preventDefault();

        PeriodicityMaster.style.display = "block";  

        const periodicity = this.dataset.periodicitycode;
        const category = this.dataset.category;

        const kpiSpoc = this.dataset.kpispoc;
        const immediateSuperior = this.dataset.immediatesuperior;
        const hod = this.dataset.hod;

        document.getElementById("PeriodicityCode").value = periodicity;
        document.getElementById("PeriodicityId").value = this.dataset.id;

        togglePeriodicityFields(); 

        if (periodicity === "Monthly" || periodicity === "Yearly") {
            document.getElementById("KpiSPOC").value = toDateTimeLocal(kpiSpoc);
            document.getElementById("ImmediateSuperior").value = toDateTimeLocal(immediateSuperior);
            document.getElementById("HOD").value = toDateTimeLocal(hod);
        }

        if (periodicity === "Quarterly") {

    const quarterMap = {
        "Apr - Jun (Q1 - 1st Qtr)": "Q1",
        "Jul - Sep (Q2 - 2nd Qtr)": "Q2",
        "Oct - Dec (Q3 - 3rd Qtr)": "Q3",
        "Jan - Mar (Q4 - 4th Qtr)": "Q4"
    };

    const q = quarterMap[category];

    if (q) {

        showOnlyQuarter(q);

        document.getElementById(`${q}_KPISPOC`).value = toDateTimeLocal(kpiSpoc);
        document.getElementById(`${q}_ImmediateSuperior`).value = toDateTimeLocal(immediateSuperior);
        document.getElementById(`${q}_HOD`).value = toDateTimeLocal(hod);
    }
}
    });
});
