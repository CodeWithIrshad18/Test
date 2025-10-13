I have this controller code 

    [Authorize(Policy = "CanRead")]
    public async Task<IActionResult> CreateKPI(Guid? id, int page = 1, string searchString = "")
    {
        if (HttpContext.Session.GetString("Session") != null)
        {
            var UserId = HttpContext.Session.GetString("Session");
            ViewBag.user = User;

            if (string.IsNullOrEmpty(UserId))
                return RedirectToAction("AccessDenied", "TPR");

            string formName = "CreateKPI";
            var form = await context.AppFormDetails
                .Where(f => f.FormName == formName)
                .Select(f => f.Id)
                .FirstOrDefaultAsync();

            if (form == default)
                return RedirectToAction("AccessDenied", "TPR");

            bool canModify = await context.AppUserFormPermissions
                .Where(p => p.UserId == UserId && p.FormId == form)
                .AnyAsync(p => p.AllowModify == true);
            bool canDelete = await context.AppUserFormPermissions
                .Where(p => p.UserId == UserId && p.FormId == form)
                .AnyAsync(p => p.AllowDelete == true);
            bool canWrite = await context.AppUserFormPermissions
                .Where(p => p.UserId == UserId && p.FormId == form)
                .AnyAsync(p => p.AllowWrite == true);

            ViewBag.CanModify = canModify;
            ViewBag.CanDelete = canDelete;
            ViewBag.CanWrite = canWrite;

            int pageSize = 5;
            int skip = (page - 1) * pageSize;

            using (var connection = new SqlConnection(GetSAPConnectionString()))
            {
                string sqlQuery = @"
    SELECT 
        k.ID,
        k.KPIDetails,
        ps.Perspectives,
        u.UnitCode,
        k.KPILevel,
        p.PeriodicityName,
        k.Division,
        k.Department,
        g.Name,
        k.KPICode
    FROM App_KPIMaster_NOPR k
    LEFT JOIN App_PeriodicityMaster_NOPR p ON k.PeriodicityID = p.ID
    LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
    LEFT JOIN App_Prespectives_NOPR ps ON k.PerspectiveID = ps.ID
    LEFT JOIN App_TypeofKPI_NOPR t ON k.TypeofKPIID = t.ID
    LEFT JOIN App_GoodPerformance_NOPR g ON k.GoodPerformance = g.ID
    WHERE (@search IS NULL OR k.KPIDetails LIKE '%' + @search + '%' 
                        OR k.KPILevel LIKE '%' + @search + '%'
                        OR ps.Perspectives LIKE '%' + @search + '%'
                        OR u.UnitCode LIKE '%' + @search + '%')
    ORDER BY k.KPIDetails
    OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;
";

                string countQuery = @"
    SELECT COUNT(*) 
    FROM App_KPIMaster_NOPR k
    LEFT JOIN App_Prespectives_NOPR ps ON k.PerspectiveID = ps.ID
    LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
    WHERE (@search IS NULL OR k.KPIDetails LIKE '%' + @search + '%'
                        OR k.KPILevel LIKE '%' + @search + '%'
                        OR ps.Perspectives LIKE '%' + @search + '%'
                        OR u.UnitCode LIKE '%' + @search + '%');
";

                var pagedData = await connection.QueryAsync<KpiListDto>(sqlQuery, new
                {
                    search = string.IsNullOrEmpty(searchString) ? null : searchString,
                    skip,
                    take = pageSize
                });

                var totalCount = await connection.ExecuteScalarAsync<int>(countQuery, new
                {
                    search = string.IsNullOrEmpty(searchString) ? null : searchString
                });

                ViewBag.ListData2 = pagedData.ToList();
                ViewBag.CurrentPage = page;
                ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
                ViewBag.searchString = searchString;
            }



            ViewBag.Area = GetAreaDD();
            ViewBag.Type = GetKPITypeDD();
            ViewBag.Unit = GetUnitDD();
            ViewBag.Periodicity = GetPeriodicityDD();
            ViewBag.perforamnce = GetPerformanceDD();
            ViewBag.division = GetDivisionDD();

          
            using (var connection = new SqlConnection(GetConnection()))
            {
                string query2 = @"
            SELECT DISTINCT ema_exec_head_desc 
            FROM SAPHRDB.dbo.T_Empl_All
            WHERE ema_exec_head_desc IS NOT NULL
            ORDER BY ema_exec_head_desc";

                var divisions = connection.Query<Division>(query2).ToList();
                ViewBag.DivisionDropdown = divisions;
            }

            AppKpiMaster viewModel = null;
            if (id.HasValue)
            {
                viewModel = await context.AppKpiMasters.FirstOrDefaultAsync(a => a.ID == id);
            }

            return View(viewModel);
        }
        else
        {
            return RedirectToAction("Login", "User");
        }
    }


  <a href="#"
   class="refNoLink"
   data-id="@item.ID"
   data-KPIDetails="@item.KPIDetails"
   data-KPIDefination="@item.KPIDefination"
   data-KPILevel="@item.KPILevel"
   data-Perspectives="@item.Perspectives"
   data-UnitCode="@item.UnitCode"
   data-PeriodicityName="@item.PeriodicityName"   
   data-Division="@item.Division"
   data-Department="@item.Department"
   data-Section="@item.Section"
   data-GoodPerformance="@item.Name"
   data-KPICode="@item.KPICode"
   data-NoofDecimal="@item.NoofDecimal"
   data-Company="@item.Company"
   data-TypeOfKPI="@item.TypeOfKPI">            
   @item.KPIDetails

</a>

and this is my model 

 public class KpiListDto
 {
     public Guid ID { get; set; }
     public string? KPIDetails { get; set; }       // KPI
     public string? Section { get; set; }
     public string? Company { get; set; }
     public string? Perspectives { get; set; }     // Area
     public string? UnitCode { get; set; }         // UOM
     public string? KPILevel { get; set; }
     public string? PeriodicityName { get; set; }
     public string? Division { get; set; }
     public string? Department { get; set; }
     public string? Name { get; set; }             // Good Performance Name
     public string? KPICode { get; set; }

     public string? KPIDefination { get; set; }

     public int? NoofDecimal { get; set; }

     public string? TypeOfKPI { get; set; }

   
     public Guid UnitID { get; set; }
     public string? CreatedBy { get; set; }
     public Guid? PeriodicityID { get; set; }
     public string? GoodPerformance { get; set; }
     public decimal? HistoricalBest { get; set; }
     public Guid? HistoricalBestYear { get; set; }
     public decimal? TheoreticalBest { get; set; }
   
     public Guid? PerspectiveID { get; set; }
     public Guid? TypeofKPIID { get; set; }
     public Guid? LTPSTPID { get; set; }
     public string? Central_Local { get; set; }
     public string? KPIUpto { get; set; }
     public bool? Deactivate { get; set; }
     public DateTime? DeactivateOn { get; set; }
  
     public string? KPIMode { get; set; }
     public string? SourceData { get; set; }
     public string? COMode { get; set; }
     public string? PCNCode { get; set; }
 }

this is my form 

    <form asp-action="CreateKPI" asp-controller="TPR" id="form" method="post" style="display:none;">
        <div class="card card-custom mt-4">
            <div class="card-header-custom">Creation of New KPI</div>
            <div class="card-body">
                <div class="row g-3">
                    <input type="hidden" asp-for="ID" id="KPIID" />
                    <input type="hidden" id="actionType" name="actionType" />

                    <div class="col-md-1">
                        <label for="KPICode" class="control-label">KPI Code</label>
                        </div>

                    <div class="col-md-3">
                        
                        <input asp-for="KPICode" class="form-control form-control-sm" id="KPICode" autocomplete="off">
                    </div>

                     <div class="col-md-1">
                        <label for="KPILevel" class="control-label">KPI Level</label>
                        </div>

                    <div class="col-md-3">
                       
                        <select asp-for="KPILevel" class="form-control form-control-sm custom-select" id="KPILevel">
                            <option></option>
                            <option value="L1">L1</option>
                            <option value="L2">L2</option>
                            <option value="L3">L3</option>
                            <option value="L4">L4</option>
                        </select>
                    </div>

                     <div class="col-md-1">
                       <label for="Company" class="control-label">Company</label>
                        </div>
                     <div class="col-md-3">
                        <select asp-for="Company" class="form-control form-control-sm custom-select" id="Company">
                            <option></option>
                            <option value="TATA STEEL UTILITIES AND INFRASTRUCTURE SERVICES LIMITED">Tata Steel Utility  & Infrastructure Services Ltd.</option>  
                        </select>
                    </div>

                    
                </div>
               <div class="row g-3 mt-1">

  
    <div class="col-md-1">
        <label for="Division" class="control-label">Division</label>
    </div>
    <div class="col-md-3">
        <div class="dropdown">
            <input class="dropdown-toggle form-control form-control-sm custom-select" type="button"
                   id="divisionDropdown" data-bs-toggle="dropdown" aria-expanded="false"/>
            <ul class="dropdown-menu w-100" aria-labelledby="divisionDropdown" id="divisionList">
                @foreach (var item in ViewBag.DivisionDropdown as List<Division>)
                {
                    <li style="margin-left:5%;">
                        <div class="form-check">
                            <input type="checkbox" class="form-check-input division-checkbox"
                                   value="@item.ema_exec_head_desc" id="div_@item.ema_exec_head_desc" />
                            <label class="form-check-label" for="div_@item.ema_exec_head_desc">
                                @item.ema_exec_head_desc
                            </label>
                        </div>
                    </li>
                }
            </ul>
        </div>
        <input type="hidden" id="Division" name="Division" />
    </div>

    <div class="col-md-1">
        <label for="Department" class="control-label">Department</label>
    </div>
    <div class="col-md-3">
        <div class="dropdown">
            <input class="dropdown-toggle form-control form-control-sm custom-select" type="button"
                   id="departmentDropdown" data-bs-toggle="dropdown" aria-expanded="false"/>
            <ul class="dropdown-menu w-100" aria-labelledby="departmentDropdown" id="departmentList"></ul>
        </div>
        <input type="hidden" id="Department" name="Department" />
    </div>

 
    <div class="col-md-1">
        <label for="Section" class="control-label">Section</label>
    </div>
    <div class="col-md-3">
        <div class="dropdown">
            <input class="dropdown-toggle form-control form-control-sm custom-select" type="button"
                   id="sectionDropdown" data-bs-toggle="dropdown" aria-expanded="false" />
            <ul class="dropdown-menu w-100" aria-labelledby="sectionDropdown" id="sectionList"></ul>
        </div>
        <input type="hidden" id="Section" name="Section" />
    </div>

</div>

                    <div class="row g-3 mt-1">
                         <div class="col-md-1">

                        <label for="PerspectiveID" class="control-label">Area</label>

                        </div>
                         <div class="col-md-3">
                        <select asp-for="PerspectiveID" class="form-control form-control-sm custom-select" name="PerspectiveID">
						<option value=""></option>
						@foreach (var item in AreaDropdown)
						{
							<option value="@item.ID">@item.Perspectives</option>
						}
						
						</select>
                        </div>

                        <div class="col-md-1">

                        <label for="TypeofKPIID" class="control-label">Type of KPI</label>

                        </div>

                         <div class="col-md-3">
                        <select asp-for="TypeofKPIID" class="form-control form-control-sm custom-select" name="TypeofKPIID">
						<option value=""></option>
						@foreach (var item in KPIDropdown)
						{
							<option value="@item.ID">@item.TypeofKPI</option>
						}
						
						</select>
                        </div>

                         <div class="col-md-1">

                        <label for="UnitID" class="control-label">Unit</label>

                        </div>

                         <div class="col-md-3">
                        <select asp-for="UnitID" class="form-control form-control-sm custom-select" name="UnitID">
						<option value=""></option>
						@foreach (var item in UnitDropdown)
						{
							<option value="@item.ID">@item.UnitCode</option>
						}
						
						</select>
                        </div>
                        </div>

                         <div class="row g-3 mt-1">
                         <div class="col-md-1">

                        <label for="KPIDefination" class="control-label">Defination</label>

                        </div>
                         <div class="col-md-3">
                        <textarea asp-for="KPIDefination" class="form-control form-control-sm" autocomplete="off" id="KPIDefination" name="KPIDefination" style="height:50%;"></textarea>
                        </div>

                        <div class="col-md-1">

                        <label for="KPIDetails" class="control-label">KPI</label>

                        </div>
                         <div class="col-md-3">
                        <textarea asp-for="KPIDetails" class="form-control form-control-sm" autocomplete="off" id="KPIDetails" name="KPIDetails" style="height:50%;"></textarea>
                        </div>

                         <div class="col-md-1">
                        <label for="PeriodicityID" class="control-label">Reporting Periodicity</label>

                        </div>

                         <div class="col-md-3">

                        <select asp-for="PeriodicityID" class="form-control form-control-sm custom-select" name="PeriodicityName">
						<option value=""></option>
						@foreach (var item in PeriodicityDropdown)
						{
							<option value="@item.ID">@item.PeriodicityCode</option>
						}
						
						</select>
                        
                        </div>
                        </div>

                         <div class="row g-3">
                             <div class="col-md-1">
                       <label for="GoodPerformance" class="control-label">Good Performance</label>
                        </div>
                     <div class="col-md-3">
                       <select asp-for="GoodPerformance" class="form-control form-control-sm custom-select" name="GoodPerformance">
						<option value=""></option>
						@foreach (var item in perforamnceDropdown)
						{
							<option value="@item.ID">@item.Name</option>
						}
						
						</select>
                    </div>

                    <div class="col-md-1">
                        <label for="NoofDecimal" class="control-label">No of Decimal</label>
                        </div>

                    <div class="col-md-3">
                        
                        <input asp-for="NoofDecimal" class="form-control form-control-sm" id="NoofDecimal" autocomplete="off">
                    </div>
                             </div>

                <div class="text-center mt-4">
                    @if (ViewBag.CanModify == true || ViewBag.CanWrite == true)
                    {
                        <button class="btn btn-primary me-2 px-4" id="submitButton" type="submit">Submit</button>
                    }
                    @if (ViewBag.CanDelete == true)
                    {
                        <button class="btn btn-danger px-4" id="deleteButton" style="display:none;">Delete</button>
                    }
                </div>
            </div>
        </div>
    </form>

and this is my js 

<script>

    document.addEventListener("DOMContentLoaded", function () {
        var newButton = document.getElementById("newButton");
        var KPIMaster = document.getElementById("form");
        var refNoLinks = document.querySelectorAll(".refNoLink");
        var deleteButton = document.getElementById("deleteButton");
        var submitButton = document.getElementById("submitButton");
        var actionTypeInput = document.getElementById("actionType");

        if (newButton) {
            newButton.addEventListener("click", function () {
                KPIMaster.style.display = "block";
                document.getElementById("KPICode").value = "";
                document.getElementById("KPILevel").value = "";
                document.getElementById("Company").value = "";            
                document.getElementById("Division").value = "";            
                document.getElementById("Department").value = "";            
                document.getElementById("Section").value = "";            
                document.getElementById("PerspectiveID").value = "";            
                document.getElementById("UnitID").value = "";            
                document.getElementById("KPIDefination").value = "";            
                document.getElementById("KPIDetails").value = "";            
                document.getElementById("PeriodicityName").value = "";            
                document.getElementById("GoodPerformance").value = "";            
                document.getElementById("NoofDecimal").value = "";                   
                document.getElementById("TypeofKPI").value = "";
                deleteButton.style.display = "none";
            });
        }

         refNoLinks.forEach(link => {
        link.addEventListener("click", function (event) {
            event.preventDefault();

         
            KPIMaster.style.display = "block";

          
            setValue("KPIID", this.getAttribute("data-id"));
            setValue("KPICode", this.getAttribute("data-KPICode"));
            setValue("KPIDetails", this.getAttribute("data-KPIDetails"));
            setValue("KPIDefination", this.getAttribute("data-KPIDefination"));
            setValue("KPILevel", this.getAttribute("data-KPILevel"));
            setValue("Division", this.getAttribute("data-Division"));
            setValue("Department", this.getAttribute("data-Department"));
            setValue("Section", this.getAttribute("data-Section"));
            setValue("NoofDecimal", this.getAttribute("data-NoofDecimal"));
            

           
          selectDropdownByText("PerspectiveID", this.getAttribute("data-Perspectives"));
selectDropdownByText("UnitID", this.getAttribute("data-UnitCode"));
selectDropdownByText("PeriodicityID", this.getAttribute("data-PeriodicityName")); 
selectDropdownByText("GoodPerformance", this.getAttribute("data-GoodPerformance"));
selectDropdownByText("TypeofKPIID", this.getAttribute("data-TypeOfKPI"));         
selectDropdownByText("Company", this.getAttribute("data-Company"));

         
            if (deleteButton) {
                deleteButton.style.display = "inline-block";
            }

            KPIMaster.scrollIntoView({ behavior: "smooth" });
        });
    });

    
    function setValue(id, value) {
        const el = document.getElementById(id);
        if (el) el.value = value ?? "";
    }
    function selectDropdownByText(id, textValue) {
        if (!textValue) return;
        const dropdown = document.getElementById(id);
        if (!dropdown) return;

        const options = Array.from(dropdown.options);
        const match = options.find(
            opt => opt.text.trim().toLowerCase() === textValue.trim().toLowerCase()
        );
        if (match) {
            dropdown.value = match.value;
        } else {
          
            const idMatch = options.find(opt => opt.value === textValue);
            if (idMatch) dropdown.value = idMatch.value;
        }
    }

        submitButton.addEventListener("click", function () {
            actionTypeInput.value = "save";  
        });

        if (deleteButton) {
    deleteButton.addEventListener("click", function () {
        Swal.fire({
            title: 'Are you sure?',
            text: "Do you really want to delete this Unit?",
            icon: 'warning',
            showCancelButton: true,
            confirmButtonColor: '#3085d6',
            cancelButtonColor: '#d33',
            confirmButtonText: 'Yes, delete it!',
            cancelButtonText: 'Cancel'
        }).then((result) => {
            if (result.isConfirmed) {
                actionTypeInput.value = "delete";  
                document.getElementById("form").submit();  
            }
        });
    });
}

    });




</script>


i want that when i click on refNoLink it populates data on the fields of the form 
