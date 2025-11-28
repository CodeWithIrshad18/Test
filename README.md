
 public List<AppSourceMaster> GetSourceDD()
 {
     string connectionString = GetConnection();

     string query = @"select distinct ID,SourceName from App_SourceMaster where SourceName is not null";


     using (var connection = new SqlConnection(connectionString))
     {
         var Source = connection.Query<AppSourceMaster>(query).ToList();

         return Source;

     }

 }


            <div class="col-md-2">
    <label for="SourceName" class="control-label">Source Name</label>
</div>
<div class="col-md-2">
    <div class="dropdown">
        <input class="dropdown-toggle form-control form-control-sm custom-select text-start" type="button"
               id="SourceDropdown" data-bs-toggle="dropdown" aria-expanded="false"/>
        <ul class="dropdown-menu w-100" aria-labelledby="SourceDropdown" id="SourceList">
            @foreach (var item in ViewBag.SourceDropdown as List<AppSourceMaster>)
            {
                <li style="margin-left:5%;">
                    <div class="form-check">
                        <input type="checkbox" class="form-check-input Source-checkbox"
                               value="@item.ID" id="div_@item.SourceName" />
                        <label class="form-check-label" for="div_@item.SourceName">
                            @item.SourceName
                        </label>
                    </div>
                </li>
            }
        </ul>
    </div>
    <input type="hidden" id="SourceName" name="SourceName" />
</div>


 ViewBag.source = GetSourceDD();
