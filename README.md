see the issue , existing data is not populating when clicking on refNoLink

                    <td>
    <a href="#" 
       class="refNoLink"
       data-id="@item.ID"
       data-KPIDetails="@item.KPIDetails"
       data-KPILevel="@item.KPILevel"
       data-Perspectives="@item.Perspectives"
       data-UnitCode="@item.UnitCode"
       data-PeriodicityName="@item.PeriodicityName"
       data-Division="@item.Division"
       data-Department="@item.Department"
       data-GoodPerformance="@item.Name"
       data-KPICode="@item.KPICode"
    data-KPIDefination="@item.KPIDefination"
    data-NoofDecimal="@item.NoofDecimal">
       @item.KPIDetails
    </a>
</td>

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

                        <select asp-for="PeriodicityID" class="form-control form-control-sm custom-select" name="PeriodicityID">
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
                document.getElementById("PeriodicityID").value = "";            
                document.getElementById("GoodPerformance").value = "";            
                document.getElementById("NoofDecimal").value = "";                   
                document.getElementById("TypeofKPIID").value = "";
                deleteButton.style.display = "none";
            });
        }

        refNoLinks.forEach(link => {
            link.addEventListener("click", function (event) {
                event.preventDefault();
                KPIMaster.style.display = "block";

                document.getElementById("KPICode").value = this.getAttribute("data-KPICode");
                document.getElementById("KPILevel").value = this.getAttribute("data-KPILevel");
                document.getElementById("Company").value = this.getAttribute("data-Company");
                document.getElementById("Division").value = this.getAttribute("data-Division");
                document.getElementById("Department").value = this.getAttribute("data-Department");
                document.getElementById("Section").value = this.getAttribute("data-Section");
                document.getElementById("PerspectiveID").value = this.getAttribute("data-PerspectiveID");
                document.getElementById("UnitID").value = this.getAttribute("data-UnitID");
                document.getElementById("KPIDefination").value = this.getAttribute("data-KPIDefination");
                document.getElementById("KPIDetails").value = this.getAttribute("data-KPIDetails");
                document.getElementById("PeriodicityID").value = this.getAttribute("data-PeriodicityID");
                document.getElementById("GoodPerformance").value = this.getAttribute("data-GoodPerformance");
                document.getElementById("NoofDecimal").value = this.getAttribute("data-NoofDecimal");
                document.getElementById("TypeofKPIID").value = this.getAttribute("data-TypeofKPIID");
                document.getElementById("KPIID").value = this.getAttribute("data-id");

                if (deleteButton) {
                    deleteButton.style.display = "inline-block";
                }
            });
        });
