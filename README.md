this is my controller code     
public async Task<IActionResult> PositionMaster(Guid? id, AppPositionWorksite appPosition, int page = 1, string searchValue = "",string PositionId ="",string WorksiteName="")
   {
       var UserId = HttpContext.Request.Cookies["Session"];

       var allowedPermissions = await context.AppPermissionMasters
               .Where(x => x.Pno == UserId)
                     .Select(x => new PermissionInfo
                     {
                         Pno = x.Pno,
                         Type = x.Type
                     })
                     .FirstOrDefaultAsync();

       if (allowedPermissions == null || allowedPermissions.Type == "Coordinator")
       {
           return RedirectToAction("AccessDenied", "Master");
       }


       int pageSize = 5;
       var query = context.AppPositionWorksites.AsQueryable();

       var position = context.AppEmpPositions
           .Where(e => e.Pno == searchValue)
           .Select(e => e.Position)
           .FirstOrDefault();


       var Worksite = context.AppLocationMasters.Select(x => x.WorkSite).ToList();
       var Benefitdd = Worksite.Select(name => new SelectListItem
       {
           Value = name,
           Text = name
       }).ToList();
       ViewBag.DDList = Benefitdd;



       if (!string.IsNullOrEmpty(position?.ToString()))
       {
           query = query.Where(p => p.Position == position);
       }
       if (!string.IsNullOrEmpty(PositionId))
       {
           query = query.Where(p => p.Position.ToString().Contains(PositionId));
       }
       if (!string.IsNullOrEmpty(WorksiteName))
       {
           query = query.Where(p => p.Worksite.Contains(WorksiteName));
       }

       var pagedData = query.Skip((page - 1) * pageSize).Take(pageSize).ToList();
       var totalCount = query.Count();

       var WorksiteList = context.AppLocationMasters
           .Select(x => new SelectListItem
           {
               Value = x.Id.ToString(),
               Text = x.WorkSite
           }).Distinct().OrderBy(x => x.Text).ToList();

       ViewBag.WorksiteDDList = new SelectList(WorksiteList, "Value", "Text", WorksiteName);

       var worksiteDictionary = context.AppLocationMasters
.ToDictionary(x => x.Id.ToString().ToLower(), x => x.WorkSite);

       foreach (var item in pagedData)
       {
           if (!string.IsNullOrWhiteSpace(item.Worksite))
           {
               var ids = item.Worksite
                   .Split(',', StringSplitOptions.RemoveEmptyEntries)
                   .Select(id => id.Trim().ToLower())
                   .ToList();

               var names = ids
                   .Where(id => worksiteDictionary.ContainsKey(id))
                   .Select(id => worksiteDictionary[id])
                   .ToList();

               item.Worksite = names.Count > 0 ? string.Join(", ", names) : "(Invalid Worksite IDs)";
           }
           else
           {
               item.Worksite = "(No Worksite)";
           }
       }

       ViewBag.pList = pagedData;
       ViewBag.CurrentPage = page;
       ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
       ViewBag.SearchValue = searchValue;
       ViewBag.PositionID = PositionId;
       ViewBag.WorksiteName = WorksiteName;



       var WorksiteList2 = context.AppEmpPositions
           .Select(x => new SelectListItem
           {
               Value = x.Position.ToString(),
               Text = x.Position.ToString()
           }).ToList();

       ViewBag.PositionDDList = WorksiteList2;

       if (id.HasValue)
       {
           var model = await context.AppPositionWorksites.FindAsync(id.Value);
           if (model == null)
           {
               return NotFound();
           }

           return Json(new
           {
               id = model.Id,
               position = model.Position,
               worksite = model.Worksite,
               createdby = UserId,
               createdon = model.CreatedOn,
           });
       }

       return View(new AppPositionWorksite());
   }

this is my grid 

<table class="table" id="myTable">
     <thead class="table" style="background-color: #d2b1ff;color: #000000;">
         <tr>
             <th width="10%">Position Id</th>

             <th>Worksite</th>

         </tr>
     </thead>
     <tbody>
         @if (ViewBag.pList != null)
         {
             @foreach (var item in ViewBag.pList)
             {
                 <tr>
                     <td>
                         <a href="javascript:void(0);" data-id="@item.Id" class="OpenFilledForm btn gridbtn" style="text-decoration:none;background-color:;font-weight:bolder;">
                             @item.Position
                         </a>
                     </td>

                     <td>@item.Worksite</td>


                 </tr>
             }
         }
         else
         {
             <tr>
                 <td colspan="3">No data available</td>
             </tr>
         }
     </tbody>
 </table>

I want to show Pno in grid
