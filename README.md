controller side   
public async Task<IActionResult> Interruption(Guid? id, int page = 1, string searchString = "")
  {
      if (HttpContext.Session.GetString("Session") != null)
      {
          var UserId = HttpContext.Session.GetString("Session");

          ViewBag.user = User;


          var userIdString = HttpContext.Session.GetString("Session");
          if (string.IsNullOrEmpty(userIdString))
          {
              return RedirectToAction("AccessDenied", "TPR");
          }



          int pageSize = 5;
          var query = context.AppInterruptions.AsQueryable();

          if (!string.IsNullOrEmpty(searchString))
          {
              query = query.Where(a => a.TypeOfInterruption.Contains(searchString));
          }

          var pagedData = await query.Skip((page - 1) * pageSize).Take(pageSize).ToListAsync();
          var totalCount = query.Count();

          ViewBag.ListData2 = pagedData;
          ViewBag.CurrentPage = page;
          ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
          ViewBag.searchString = searchString;

          ViewBag.Source = GetSourceDD();
          ViewBag.feeder = GetFeederDD();
          ViewBag.DTR = GetDTRDD();


          AppInterruption viewModel = null;

          if (id.HasValue)
          {
              viewModel = await context.AppInterruptions.FirstOrDefaultAsync(a => a.ID == id);
          }

          return View(viewModel);
      }
      else
      {
          return RedirectToAction("Login", "User");
      }
  }

dropdowns query


  public List<AppSourceMaster> GetSourceDD()
  {
      string connectionString = GetConnection();

      string query = @"select ID,SourceName from App_SourceMaster";


      using (var connection = new SqlConnection(connectionString))
      {
          var source = connection.Query<AppSourceMaster>(query).ToList();

          return source;

      }

  }

  public List<AppFeederMaster> GetFeederDD()
  {
      string connectionString = GetConnection();

      string query = @"select ID,FeederName from App_FeederMaster";


      using (var connection = new SqlConnection(connectionString))
      {
          var feeder = connection.Query<AppFeederMaster>(query).ToList();

          return feeder;

      }

  }

  public List<AppDTRMaster> GetDTRDD()
  {
      string connectionString = GetConnection();

      string query = @"select ID,DTRName from App_DTRMaster";


      using (var connection = new SqlConnection(connectionString))
      {
          var DTR = connection.Query<AppDTRMaster>(query).ToList();

          return DTR;

      }

  }


view side of Dropdown

  <div class="row g-3 d-flex">
      <input type="hidden" asp-for="ID" id="DTRId" name="ID" />
      <input type="hidden" id="actionType" name="actionType" />

      <div class="col-md-1">
          <label for="SourceID" class="control-label">Source</label>
      </div>
      <div class="col-md-2">
          <select asp-for="SourceID" class="form-control form-control-sm custom-select" name="SourceID">
              <option value=""></option>
              @foreach (var item in SourceDropdown)
              {
                  <option value="@item.ID">@item.SourceName</option>
              }

          </select>
      </div>

      <div class="col-md-1">
          <label for="FeederID" class="control-label">Feeder</label>
      </div>
      <div class="col-md-2">
          <select asp-for="FeederID" class="form-control form-control-sm custom-select" name="FeederID">
              <option value=""></option>
              @foreach (var item in feederDropdown)
              {
                  <option value="@item.ID">@item.FeederName</option>
              }

          </select>
      </div>

      <div class="col-md-1">
          <label for="FeederID" class="control-label">DTR </label>
      </div>
      <div class="col-md-2">
          <select asp-for="DTRID" class="form-control form-control-sm custom-select" name="DTRName">
              <option value=""></option>
              @foreach (var item in DTRDropdown)
              {
                  <option value="@item.ID">@item.DTRName</option>
              }

          </select>
      </div>
      <div class="col-md-1">
          <label for="DTRCapacity" class="control-label">DTR Capacity</label>
      </div>
      <div class="col-md-2">
          <input asp-for="DTRCapacity" class="form-control form-control-sm" id="DTRCapacity" type="number" autocomplete="off">
      </div>
  </div>
  <div class="row g-3 mt-1">
      <div class="col-md-1">
          <label for="NoOfConsumer" class="control-label">No Of Consumer</label>
      </div>
      <div class="col-md-2">
          <input asp-for="NoOfConsumer" class="form-control form-control-sm" id="NoOfConsumer" type="number" autocomplete="off">
      </div>

      <div class="col-md-1">
          <label for="FeederID" class="control-label">Type Of Interruption </label>
      </div>
      <div class="col-md-2">
          <select asp-for="TypeOfInterruption" class="form-control form-control-sm custom-select" name="TypeOfInterruption">
              <option value=""></option>                          
              <option value="Breakdown">Breakdown</option>
              <option value="Shutdown">Shutdown</option>
              <option value="Loadshedding">Loadshedding</option>
          </select>
      </div>
      <div class="col-md-1">
          <label for="FromDate" class="control-label">From Date</label>
      </div>
      <div class="col-md-2">
          <input asp-for="FromDate" type="datetime-local" class="form-control form-control-sm" id="FromDate" name="FromDate">
      </div>
      <div class="col-md-1">
          <label for="ToDate" class="control-label">To Date</label>
      </div>
      <div class="col-md-2">
          <input asp-for="ToDate" type="datetime-local" class="form-control form-control-sm" id="ToDate" name="ToDate">
      </div>
  </div>



 select * from App_DTRMaster where SourceID ='' and FeederID ='' and SourceID=''
