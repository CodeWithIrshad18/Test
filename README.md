   [Authorize(Policy = "CanRead")]
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

and this is my UserFormPermission
public partial class AppUserFormPermission
{
    public Guid Id { get; set; }
    public string? UserId { get; set; }
    public Guid FormId { get; set; }
    public bool AllowRead { get; set; }
    public bool AllowWrite { get; set; }
    public bool? AllowDelete { get; set; }
    public bool? AllowAll { get; set; }
    public bool? AllowModify { get; set; }
    public bool DownTime { get; set; }
}

ant this is my form Details 
 public partial class AppFormDetail
 {
     public Guid Id { get; set; }
     public string? FormName { get; set; }
     public string? Description { get; set; }
 }

this is my table 

         <div class="card m-2 shadow-lg">
    <div class="card-header text-light" style="background-color:#49477a;color:white;font-weight:bold;">
        <h6 class="m-0">User Authorization List</h6>
    </div>
           <table class="table table-hover mb-0">
    <thead>
        <tr>
            <th>P.No.</th> 
 <th>Form Name</th> 
            <th>Read</th> 
            <th>Create</th>
            <th>Update</th>
             <th>Delete</th>
            <th>All</th>
        </tr>
    </thead>
   @*  <tbody>
        @if (ViewBag.ListData2 != null)
        {
            @foreach (var item in ViewBag.ListData2)
            {
                <tr>
                    <td>
   <a href="#"
   class="refNoLink"
   data-id="@item.ID">
   @item.KPIDetails
</a>

</td>
                   
               
                    <td>@item.UnitCode</td>
                     <td>@item.Division</td>
                    <td>@item.Department</td>
                    <td>@item.Section</td> 
                    <td>@item.KPICode</td>
                </tr>
            }
        }
        else
        {
            <tr>
                <td colspan="9" class="text-center text-muted py-3">No data available</td>
            </tr>
        }
    </tbody> *@

     <tbody>

i want to show total records 
