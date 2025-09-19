// search text (Pno or Name)
if (!string.IsNullOrEmpty(searchString))
{
    empList = empList
        .Where(e =>
            e.Pno != null && e.Pno.ToString().Contains(searchString, StringComparison.OrdinalIgnoreCase) ||
            e.Ema_Ename != null && e.Ema_Ename.ToString().Contains(searchString, StringComparison.OrdinalIgnoreCase)
        )
        .ToList();
}

// filter by PositionId
if (!string.IsNullOrEmpty(PositionId))
{
    empList = empList
        .Where(e => e.Position != null && e.Position.ToString().Contains(PositionId, StringComparison.OrdinalIgnoreCase))
        .ToList();
}

// filter by Worksite
if (!string.IsNullOrEmpty(WorksiteName))
{
    empList = empList
        .Where(e => e.Worksites != null && e.Worksites.ToString().Contains(WorksiteName, StringComparison.OrdinalIgnoreCase))
        .ToList();
}




// search text (Pno or Name)
if (!string.IsNullOrEmpty(searchString))
{
    empList = empList
        .Where(e =>
            ((string)e.Pno).Contains(searchString, StringComparison.OrdinalIgnoreCase) ||
            (!string.IsNullOrEmpty((string)e.Ema_Ename) && ((string)e.Ema_Ename).Contains(searchString, StringComparison.OrdinalIgnoreCase))
        )
        .ToList();
}

// filter by PositionId
if (!string.IsNullOrEmpty(PositionId))
{
    empList = empList
        .Where(e => !string.IsNullOrEmpty((string)e.Position) &&
                    ((string)e.Position).Contains(PositionId, StringComparison.OrdinalIgnoreCase))
        .ToList();
}

// filter by Worksite
if (!string.IsNullOrEmpty(WorksiteName))
{
    empList = empList
        .Where(e => !string.IsNullOrEmpty((string)e.Worksites) &&
                    ((string)e.Worksites).Contains(WorksiteName, StringComparison.OrdinalIgnoreCase))
        .ToList();
}




this is my full controller code
        [HttpGet]
        public async Task<IActionResult> EmpTaggingMaster(string searchString = "", int page = 1,string WorksiteName="",string PositionId="")
        {
            var UserId = HttpContext.Request.Cookies["Session"];
            if (string.IsNullOrEmpty(UserId))
                return RedirectToAction("Login", "User");


            var allowedPermissions = await context.AppPermissionMasters
                    .Where(x => x.Pno == UserId)
                          .Select(x => new PermissionInfo
                          {
                              Pno = x.Pno,
                              Type = x.Type
                          })
                          .FirstOrDefaultAsync();

            if (allowedPermissions == null)
            {
                return RedirectToAction("AccessDenied", "Master");
            }

            string connectionString = GetRFIDConnectionString();


            var WorksiteList = context.AppLocationMasters
               .Select(x => new SelectListItem
               {
                   Value = x.Id.ToString(),
                   Text = x.WorkSite
               }).Distinct().OrderBy(x => x.Text).ToList();

            ViewBag.WorksiteDDList = new SelectList(WorksiteList, "Value", "Text", WorksiteName);

            List<dynamic> empList = new();
            List<SelectListItem> worksiteList = new();
            List<SelectListItem> pnoDropdown = new();

            using (var conn = new SqlConnection(connectionString))
            {
                await conn.OpenAsync();


                string deptSql = "SELECT DeptName FROM App_CoordinatorMaster WHERE Pno = @Pno";
                var department = await conn.QueryFirstOrDefaultAsync<string>(deptSql, new { Pno = UserId });



                string dataSql = @"
             SELECT 
    ep.Pno,
    e.Ema_Ename,
    ep.Position,
    pw.CreatedBy,
    pw.CreatedOn,
    STRING_AGG(sites.Work_Site, ', ') AS Worksites
FROM SAPHRDB.dbo.T_Empl_All e
Inner JOIN App_Emp_Position ep ON e.Ema_perno = ep.Pno
Inner JOIN App_Position_Worksite pw ON ep.Position = pw.Position
OUTER APPLY (
    SELECT l.Work_Site
    FROM STRING_SPLIT(pw.Worksite, ',') AS split
    JOIN App_LocationMaster l ON TRY_CAST(split.value AS uniqueidentifier) = l.ID
) AS sites
WHERE (ema_pyrl_area = 'ZZ' or ema_pyrl_area = 'JS') and  ema_dept_desc IN (SELECT value FROM STRING_SPLIT(@DeptName, ';'))
GROUP BY ep.Pno, e.Ema_Ename, ep.Position,pw.CreatedBy,pw.CreatedOn
ORDER BY ep.Pno";

                empList = (await conn.QueryAsync(dataSql, new { DeptName = department })).ToList();


                string pnoSql = @"
    SELECT 
        Ema_perno, 
        Ema_perno + ' - ' + ema_ename AS ema_ename
    FROM 
        SAPHRDB.dbo.T_Empl_All 
    WHERE 
        (ema_pyrl_area = 'ZZ' OR ema_pyrl_area = 'JS') 
        AND ema_dept_desc IN (SELECT value FROM STRING_SPLIT(@DeptName, ';'))
";
                var rawPnos = await conn.QueryAsync<EmployeeDropdownItem>(pnoSql, new { DeptName = department });

                pnoDropdown = rawPnos.Select(p => new SelectListItem
                {
                    Value = p.Ema_perno,
                    Text = p.Ema_ename
                }).ToList();


                string wsQuery = "SELECT ID, Work_Site FROM App_LocationMaster order by Work_Site";
                var worksites = await conn.QueryAsync(wsQuery);
                worksiteList = worksites.Select(ws => new SelectListItem
                {
                    Value = ws.ID.ToString(),
                    Text = ws.Work_Site.ToString()
                }).ToList();
            }


            if (!string.IsNullOrEmpty(searchString))
            {
                empList = empList
                    .Where(e => ((string)e.Pno).Contains(searchString, StringComparison.OrdinalIgnoreCase)
                             || (!string.IsNullOrEmpty((string)e.Name) && ((string)e.Name).Contains(searchString, StringComparison.OrdinalIgnoreCase)))
                    .ToList();
            }

            if (!string.IsNullOrEmpty(PositionId))
            {
                empList = empList
                    .Where(e => !string.IsNullOrEmpty(e.PositionId) && e.PositionId.Contains(PositionId, StringComparison.OrdinalIgnoreCase))
                    .ToList();
            }

            if (!string.IsNullOrEmpty(WorksiteName))
            {
                empList = empList
                    .Where(e => e.WorksiteName == WorksiteName)
                    .ToList();
            }



            int pageSize = 5;
            var pagedData = empList.Skip((page - 1) * pageSize).Take(pageSize).ToList();


            ViewBag.pList = pagedData;
            ViewBag.PnoDropdown = pnoDropdown;
            ViewBag.WorksiteList = worksiteList;

            ViewBag.CurrentPage = page;
            ViewBag.TotalPages = (int)Math.Ceiling(empList.Count / (double)pageSize);
            ViewBag.SearchString = searchString;
            ViewBag.PositionId = PositionId;

            return View();
        }


and this is my grid 
<table class="table table-bordered" id="myTable">
	<thead class="table" style="background-color: #d2b1ff;color: #000000;">
		<tr>
			<th width="5%">P.NO.</th>
			<th width="10%">Name</th>
			<th width="8%">Position ID</th>
			<th width="25%">Worksites</th>
			<th width="10%">Last change by</th>
			<th width="15%">Last change Date</th>
		</tr>
	</thead>
	<tbody>
		@if (ViewBag.pList != null)
		{
			foreach (var item in ViewBag.pList)
			{
				<tr>
					<td>
						<a href="javascript:void(0);" data-id="@item.Id" data-Pno="@item.Pno" data-Position="@item.Position" data-Worksites="@item.Worksites"
						@item.Subject class="OpenFilledForm btn gridbtn refNoLink"
						   style="text-decoration:none;background-color:#ffffff;font-weight:bolder;color:darkblue;">
							@item.Pno
						</a>
					</td>
					<td>@item.Ema_Ename</td>
					<td>@item.Position</td>
					<td>@item.Worksites</td>
					<td>@item.CreatedBy</td>
					<td>@item.CreatedOn</td>
				</tr>
			}
		}
		else
		{
			<tr>
				<td colspan="4">No data available</td>
			</tr>
		}
	</tbody>
</table>

not working the searching 
