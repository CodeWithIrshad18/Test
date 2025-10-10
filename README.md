make this in one row 

            <div class="row g-3 mt-1">
                <div class="col-md-1">
                    <label for="Division" class="control-label">Division</label>
                    </div>

               <div class="col-sm-3">
   
    <div class="dropdown">
        <input class="dropdown-toggle form-control form-control-sm custom-select" type="button"
               id="divisionDropdown" data-bs-toggle="dropdown" aria-expanded="false" readonly />

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

                <div class="col-md-1">
                    <label for="Department" class="control-label">Department</label>
                    </div>

               <div class="col-sm-3">
    <div class="dropdown">
        <input class="dropdown-toggle form-control form-control-sm custom-select" type="button"
               id="departmentDropdown" data-bs-toggle="dropdown" aria-expanded="false" readonly />
        <ul class="dropdown-menu w-100" aria-labelledby="departmentDropdown" id="departmentList"></ul>
    </div>
    <input type="hidden" id="Department" name="Department" />
</div>

                <div class="col-md-1">
                    <label for="Section" class="control-label">Section</label>
                    </div>

               <div class="col-sm-3">
   
    <div class="dropdown">
        <input class="dropdown-toggle form-control form-control-sm custom-select" type="button"
               id="sectionDropdown" data-bs-toggle="dropdown" aria-expanded="false" readonly />
        <ul class="dropdown-menu w-100" aria-labelledby="sectionDropdown" id="sectionList"></ul>
    </div>
    <input type="hidden" id="Section" name="Section" />
</div>

                </div>
