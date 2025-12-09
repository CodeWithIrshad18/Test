    <div class="card card-custom mb-3">
        <div class="card-body">
            <div class="row align-items-center">
                <div class="col-md-10 d-flex">
                    <form method="get" action="@Url.Action("Interruption", "PS")" class="d-flex">                        
                        <select class="form-control form-control-sm custom-select" name="SourceSearch" style="height:80%;">
                            <option value=""></option>
                            @foreach (var item in SourceDropdown)
                            {
                                <option value="@item.ID">@item.SourceName</option>
                            }
                        </select>
                        <select class="form-control form-control-sm custom-select" name="FeederSearch" style="height:80%;">
                            <option value=""></option>
                            @foreach (var item in feederDropdown)
                            {
                                <option value="@item.ID">@item.FeederName</option>
                            }
                        </select>
                        <select class="form-control form-control-sm custom-select" name="DTRSearch" style="height:80%;">
                            <option value=""></option>
                            @foreach (var item in DTRDropdown)
                            {
                                <option value="@item.ID">@item.DTRName</option>
                            }
                        </select>
                        <button type="submit" class="btn btn-primary px-4 col-sm-5">Search</button>
                    </form>
                </div>
                <div class="col-md-2 text-end mt-2 mt-md-0">
                    <button type="button" class="btn btn-primary" id="newButton">
                        <i class="bi bi-plus-circle me-1"></i> New
                    </button>
                </div>
            </div>
        </div>
    </div>
