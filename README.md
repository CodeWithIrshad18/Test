<a href="#" class="refNoLink"
   data-id="@item.ID"
   data-code="@item.PeriodicityCode">
    @item.PeriodicityCode
</a>

if (id.HasValue)
{
    var record = await context.AppPeriodicityMasters
        .FirstOrDefaultAsync(x => x.ID == id);

    if (record == null)
        return View(null);

    // If Monthly / Yearly
    if (record.PeriodicityCode == "Monthly" || record.PeriodicityCode == "Yearly")
    {
        return View(record);
    }

    // If Quarterly → Load ALL 4 rows
    var allQuarterRows = await context.AppPeriodicityMasters
        .Where(x => x.PeriodicityCode == "Quarterly")
        .ToListAsync();

    ViewBag.Q1 = allQuarterRows.FirstOrDefault(q => q.Category.Contains("Q1"));
    ViewBag.Q2 = allQuarterRows.FirstOrDefault(q => q.Category.Contains("Q2"));
    ViewBag.Q3 = allQuarterRows.FirstOrDefault(q => q.Category.Contains("Q3"));
    ViewBag.Q4 = allQuarterRows.FirstOrDefault(q => q.Category.Contains("Q4"));

    return View(record);
}

<input type="datetime-local" class="form-control"
       name="Q1_KPISPOC"
       value="@(ViewBag.Q1?.KPISPOC?.ToString("yyyy-MM-ddTHH:mm"))">

<input type="datetime-local" class="form-control"
       name="Q1_ImmediateSuperior"
       value="@(ViewBag.Q1?.ImmediateSuperior?.ToString("yyyy-MM-ddTHH:mm"))">

<input type="datetime-local" class="form-control"
       name="Q1_HOD"
       value="@(ViewBag.Q1?.HOD?.ToString("yyyy-MM-ddTHH:mm"))">

refNoLinks.forEach(link => {
    link.addEventListener("click", function (e) {
        e.preventDefault();

        let code = this.getAttribute("data-code");

        document.getElementById("PeriodicityCode").value = code;

        if (code === "Quarterly") {
            document.getElementById("monthlyYearlyFields").style.display = "none";
            document.getElementById("quarterlyFields").style.display = "block";
        } else {
            document.getElementById("monthlyYearlyFields").style.display = "flex";
            document.getElementById("quarterlyFields").style.display = "none";
        }

        document.getElementById("PeriodicityId").value = this.getAttribute("data-id");
    });
});




ID	PeriodicityCode	PeriodicityName	KPISPOC	ImmediateSuperior	HOD	Category	CreatedBy	CreatedOn
CDD263FE-5946-4BD2-A2C7-B7E31C19640A	Monthly	Monthly	2025-11-07 10:15:00.000	2025-11-21 10:15:00.000	2025-11-25 10:15:00.000	Monthly	151514	NULL
983CC4F5-339B-4688-9D08-B97200024CA3	Quarterly	Quarterly	2025-11-21 12:04:00.000	2025-11-25 12:04:00.000	2025-11-26 12:04:00.000	Jan - Mar (Q4 - 4th Qtr)	151514	2025-11-27 12:05:07.617
CBDE69E6-B9FB-458D-A04B-C70B16C63C5B	Quarterly	Quarterly	2025-01-23 12:04:00.000	2025-01-20 12:04:00.000	2025-01-31 12:04:00.000	Apr - Jun (Q1 - 1st Qtr)	151514	2025-11-27 12:05:07.617
F1898CE6-E7FA-4726-9E1B-680BA4F981F9	Quarterly	Quarterly	2025-11-18 12:04:00.000	2025-11-25 12:04:00.000	2025-11-22 12:04:00.000	Oct - Dec (Q3 - 3rd Qtr)	151514	2025-11-27 12:05:07.617
A0CCBD90-449E-40C6-BDD8-82A6B67D0C29	Quarterly	Quarterly	2025-11-06 12:04:00.000	2025-11-16 12:04:00.000	2025-11-19 12:04:00.000	Jul - Sep (Q2 - 2nd Qtr)	151514	2025-11-27 12:05:07.617
D16070D5-69F0-402B-BA51-8D2909BECA11	Yearly	Yearly	2025-11-11 10:34:00.000	2025-11-18 10:34:00.000	2025-11-26 10:34:00.000	Yearly	151514	NULL
