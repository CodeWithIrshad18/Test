   <form asp-action="UserPermission" asp-controller="User" method="post">
         <div class="form" id="formContainer" style="display:none;">
       <fieldset class="mt-2" style="border:1px solid #bfbebe;padding:5px 20px 5px 20px;border-radius:6px">
           <legend class="legend"><b>Select Permission for the User</b></legend>
         
               <div class="w-100 border" style="overflow:auto;height:250px;">
                   <table class="table-hover table-responsive-sm" cellspacing="0" cellpadding="4" id="MainContent_userPermissions" style="color:#333333;width:100%;border-collapse:collapse;">
                       <tbody>
                           <tr style="color:White;background-color:#49477a;font-size:Smaller;font-weight:bold;">
                               <th align="left" scope="col">Form Name</th>
                             
                               <th scope="col">Read</th>
                               <th scope="col">Create</th>
                               <th scope="col">Update</th>
                               <th scope="col">Delete</th>
                               <th scope="col">All</th>
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
                   <input type="submit" value="Save" id="MainContent_btnSave" class="btn btn-primary btn-sm col-sm-1">
               </div>
           </div>
       </fieldset>
   </form>


           <table class="table table-hover mb-0">
    <thead>
        <tr>
            <th width="10%">P.No.</th>
            <th width="20%">Name</th>                    
        </tr>
    </thead>
   <tbody>
    @if (ViewBag.ListData2 != null)
    {
        foreach (var item in ViewBag.ListData2)
        {
            <tr>
                <td style="font-weight:bold;">@item.UserId</td>
                <td>@item.ema_ename</td>
            </tr>
        }
    }
    else
    {
        <tr>
            <td colspan="8" class="text-center text-muted py-3">No data available</td>
        </tr>
    }
</tbody>
</table>


<script>
    var pnoEnameList = @Html.Raw(JsonConvert.SerializeObject(ViewBag.PnoEnameList ?? new object[0]));
    var userPermissions = @Html.Raw(JsonConvert.SerializeObject(ViewBag.UserPermissions ?? new object[0]));

    document.addEventListener("DOMContentLoaded", function () {
        document.getElementById("Pno").addEventListener("input", function () {
            var pno = this.value;
           

            var user = pnoEnameList.find(u => u.ema_perno == pno);

            if (user) {
             
                document.getElementById("Name").value = user.ema_ename;
                document.getElementById("UserId").value = user.ema_perno;
                document.getElementById("formContainer").style.display = "block";

               
                document.querySelectorAll('input[type="checkbox"]').forEach(cb => cb.checked = false);

             
                var userPermissionsList = userPermissions.filter(p => p.UserId == user.ema_perno);
                
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
        document.querySelectorAll('input[name*="AllowAll"]').forEach(allCheckbox => {
            allCheckbox.addEventListener("change", function () {
                const row = this.closest("tr");
                if (row) {
                    const checkboxes = row.querySelectorAll('input[type="checkbox"]:not([name*="AllowAll"])');
                    checkboxes.forEach(cb => cb.checked = this.checked);
                }
            });
        });

      
        document.querySelectorAll('input[type="checkbox"]:not([name*="AllowAll"])').forEach(cb => {
            cb.addEventListener("change", function () {
                const row = this.closest("tr");
                const allCheckbox = row.querySelector('input[name*="AllowAll"]');
                const allChecked = Array.from(row.querySelectorAll('input[type="checkbox"]:not([name*="AllowAll"])')).every(c => c.checked);
                allCheckbox.checked = allChecked;
            });
        });

       
        const saveButton = document.getElementById("MainContent_btnSave");
        const form = saveButton.closest("form");

        saveButton.addEventListener("click", function (event) {
            event.preventDefault(); 

            Swal.fire({
                title: "Confirm Save",
                text: "Are you sure you want to save these permissions?",
                icon: "question",
                showCancelButton: true,
                confirmButtonColor: "#3085d6",
                cancelButtonColor: "#d33",
                confirmButtonText: "Yes, Save it!",
                cancelButtonText: "Cancel"
            }).then((result) => {
                if (result.isConfirmed) {
                    form.submit(); 
                }
            });
        });
    });
   

        document.querySelector("form").addEventListener("submit", function() {
    const searchBtn = this.querySelector("button[type='submit']");
    searchBtn.disabled = true;
    searchBtn.innerHTML = '<span class="spinner-border spinner-border-sm"></span>';
});
</script>
