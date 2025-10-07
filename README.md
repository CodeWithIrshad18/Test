<script>
    var pnoEnameList = @Html.Raw(JsonConvert.SerializeObject(ViewBag.PnoEnameList ?? new object[0]));
    var userPermissions = @Html.Raw(JsonConvert.SerializeObject(ViewBag.UserPermissions ?? new object[0]));

    document.addEventListener("DOMContentLoaded", function () {
        document.getElementById("Pno").addEventListener("input", function () {
            var pno = this.value;
            console.log("Entered PNO:", pno);
            console.log("All users:", pnoEnameList);

            var user = pnoEnameList.find(u => u.ema_perno == pno);

            if (user) {
                console.log("Matched user:", user);
                document.getElementById("Name").value = user.ema_ename;
                document.getElementById("UserId").value = user.ema_perno;
                document.getElementById("formContainer").style.display = "block";

                // Reset all checkboxes
                document.querySelectorAll('input[type="checkbox"]').forEach(cb => cb.checked = false);

                // Apply user permissions
                var userPermissionsList = userPermissions.filter(p => p.UserId == user.ema_perno);
                console.log("User permissions:", userPermissionsList);

                userPermissionsList.forEach(permission => {
                    let row = document.querySelector(`input[type="hidden"][name^="FormPermissions"][value="${permission.FormId}"]`)?.closest("tr");
                    if (row) {
                        if (permission.AllowRead) row.querySelector('input[name*="AllowRead"]').checked = true;
                        if (permission.AllowWrite) row.querySelector('input[name*="AllowWrite"]').checked = true;
                        if (permission.AllowModify) row.querySelector('input[name*="AllowModify"]').checked = true;
                        if (permission.AllowDelete) row.querySelector('input[name*="AllowDelete"]').checked = true;
                        if (permission.AllowAll) row.querySelector('input[name*="AllowAll"]').checked = true;
                    }
                });

            } else {
                document.getElementById("Name").value = "";
                document.getElementById("UserId").value = "";
                document.getElementById("formContainer").style.display = "none";
                document.querySelectorAll('input[type="checkbox"]').forEach(cb => cb.checked = false);
            }
        });
    });
</script>

var user = pnoEnameList.find(u => u.ema_perno == pno);



I have this view side code 

<div class="card m-2 shadow-lg">
    <div class="card-header text-light" style="background-color:#49477a;color:white;font-weight:bold;">
        <h6 class="m-0">User Authorization</h6>
    </div>
    <div class="card-body pt-1">
        <fieldset class="" style="border:1px solid #bfbebe;padding:5px 20px 5px 20px;border-radius:6px">
            <legend class="legend"><b>User Information</b></legend>

            <div class="form-inline row">
                <div class="col-md-1 mb-1">
                    <label class="control-label">Select User:</label>
                </div>
                <div class="col-md-2 mb-1">
                    <input type="number" id="Pno" class="form-control form-control-sm" placeholder="Max 6 digits" oninput="javascript: if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);" maxlength="6" autocomplete="off">
                </div>
                <div class="col-md-1 mb-1">
                    <label class="control-label">User Name:</label>
                </div>
                <div class="col-md-3 mb-1">
                    <input type="text" readonly id="Name" class="form-control form-control-sm">
                </div>
            </div>

        </fieldset>

       
        <form asp-action="UserPermission" asp-controller="User" method="post">
            <fieldset class="mt-2" style="border:1px solid #bfbebe;padding:5px 20px 5px 20px;border-radius:6px">
                <legend class="legend"><b>Select Permission for the User</b></legend>
                <div class="form" id="formContainer" style="display:none;">
                    <div class="w-100 border" style="overflow:auto;height:250px;">
                        <table class="table-hover table-responsive-sm" cellspacing="0" cellpadding="4" id="MainContent_userPermissions" style="color:#333333;width:100%;border-collapse:collapse;">
                            <tbody>
                                <tr style="color:White;background-color:#49477a;font-size:Smaller;font-weight:bold;">
                                    <th align="left" scope="col">Form Name</th>
                                    <th scope="col">&nbsp;</th>
                                    <th scope="col">&nbsp;</th>
                                    <th scope="col">&nbsp;</th>
                                    <th scope="col">&nbsp;</th>
                                    <th scope="col">&nbsp;</th>
                                </tr>

                                
                                @if (ViewBag.formList != null)
                                {
                                    var formList = ViewBag.formList as List<AppFormDetail>;
                                    int rowIndex = 0;

                                    @foreach (var form in formList)
                                    {
                                        string bgColor = (rowIndex % 2 == 1 && rowIndex != 0) ? "#e3dff3" : "transparent";
                                        <tr style="color:#333333; background-color:@bgColor; font-size:Smaller;">
                                            <td style="width:50%;">
                                                <input type="hidden" name="FormPermissions[@rowIndex].FormId" value="@form.Id" />
                                                <span>@form.Description</span>
                                            </td>

                                            <td style="width:100px;">
                                                <input type="checkbox" name="FormPermissions[@rowIndex].AllowRead" value="true">
                                                <label class="control-label">&nbsp;Read</label>
                                            </td>
                                            <td style="width:100px;">
                                                <input type="checkbox" name="FormPermissions[@rowIndex].AllowWrite" value="true">
                                                <label class="control-label">&nbsp;Create</label>
                                            </td>
                                            <td style="width:100px;">
                                                <input type="checkbox" name="FormPermissions[@rowIndex].AllowModify" value="true">
                                                <label class="control-label">&nbsp;Modify</label>
                                            </td>
                                            <td style="width:100px;">
                                                <input type="checkbox" name="FormPermissions[@rowIndex].AllowDelete" value="true">
                                                <label class="control-label">&nbsp;Delete</label>
                                            </td>
                                            <td style="width:100px;">
                                                <input type="checkbox" name="FormPermissions[@rowIndex].AllowAll" value="true">
                                                <label class="control-label">&nbsp;All</label>
                                            </td>
                                        </tr>
                                        rowIndex++;
                                    }
                                }
                            </tbody>
                        </table>
                    </div>

                    <div class="row m-0 justify-content-center mt-2">
                        <input type="hidden" id="UserId" name="UserId" />
                        <input type="submit" value="Save" id="MainContent_btnSave" class="btn btn-primary btn-sm">
                    </div>
                </div>
            </fieldset>
        </form>
        
    </div>
</div>



<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>

<script>
    var pnoEnameList = @Html.Raw(JsonConvert.SerializeObject(ViewBag.PnoEnameList));
    var userPermissions = @Html.Raw(JsonConvert.SerializeObject(ViewBag.UserPermissions));

  document.addEventListener("DOMContentLoaded", function () {
    document.getElementById("Pno").addEventListener("input", function () {
        var pno = this.value;
        var user = pnoEnameList.find(u => u.UserId === pno);

        if (user) {
            document.getElementById("Name").value = user.Name;
            document.getElementById("UserId").value = user.Id;
            document.getElementById("formContainer").style.display = "block";

            console.log(userPermissions);

            
            var userPermissionsList = userPermissions.filter(p => p.UserId === user.Id);
            console.log(userPermissionsList);

          
            document.querySelectorAll('input[type="checkbox"]').forEach(checkbox => {
                checkbox.checked = false;
            });

           
            userPermissionsList.forEach(permission => {
                
                let row = document.querySelector(`input[type="hidden"][name^="FormPermissions"][value="${permission.FormId}"]`)?.closest("tr");

                if (row) {
                    if (permission.AllowRead) row.querySelector('input[name^="FormPermissions"][name*="AllowRead"]').checked = true;
                    if (permission.AllowWrite) row.querySelector('input[name^="FormPermissions"][name*="AllowWrite"]').checked = true;
                    if (permission.AllowModify) row.querySelector('input[name^="FormPermissions"][name*="AllowModify"]').checked = true;
                    if (permission.AllowDelete) row.querySelector('input[name^="FormPermissions"][name*="AllowDelete"]').checked = true;
                    if (permission.AllowAll) row.querySelector('input[name^="FormPermissions"][name*="AllowAll"]').checked = true;
                }
            });

        } else {
           
            document.getElementById("Name").value = "";
            document.getElementById("UserId").value = "";
            document.getElementById("formContainer").style.display = "none";

            document.querySelectorAll('input[type="checkbox"]').forEach(checkbox => {
                checkbox.checked = false;
            });
        }
    });
});
</script>

and this is my controller 

 public IActionResult UserPermission()
 {
     string connectionString = GetSAPConnectionString();

     using (var connection = new SqlConnection(connectionString))
     {
         string query = @"
select ema_perno,ema_ename from SAPHRDB.dbo.T_Empl_All";
         var PnoEnameList = connection.Query(query).ToList();
         ViewBag.PnoEnameList = PnoEnameList;
     }

     var formList = context.AppFormDetails.Select(x => new AppFormDetail
     {
         Id = x.Id,
         Description = x.Description
     }).OrderBy(x => x.Description).ToList();
     ViewBag.formList = formList;


     var userPermissions = context.AppUserFormPermissions.ToList();
     ViewBag.UserPermissions = userPermissions;

     return View();
 }

why when I enter pno form is not opening 
