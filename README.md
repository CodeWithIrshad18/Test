<script>

document.addEventListener("change", function (e) {

    if (e.target.type === "radio" && e.target.name.indexOf("HOD_Rating") >= 0) {

        var row = e.target.closest("tr");

        var radios = row.querySelectorAll("input[type='radio']");
        var anyChecked = false;

        radios.forEach(function (r) {
            if (r.checked) anyChecked = true;
        });

        var lbl = row.querySelector("span[id*='lblMsg']");

        if (lbl)
            lbl.style.display = anyChecked ? "none" : "inline";
    }

});

</script>





<script type="text/javascript">

function ValidateHodRatings(src, args) {

    var table = document.getElementById("<%= Plandetails.ClientID %>");
    var rows = table.getElementsByTagName("tr");

    // i = 1  → header skip
    for (var i = 1; i < rows.length; i++) 
    {
        // sirf is row ke radio lo
        var radios = rows[i].querySelectorAll('input[type=radio][name*="HOD_Rating"]');

        if (radios.length === 0) continue;   // agar row me radio hi nahi hai to skip

        var checked = false;

        for (var j = 0; j < radios.length; j++) {
            if (radios[j].checked) {
                checked = true;
                break;
            }
        }

        // ❌ agar is row me kuch select nahi hai → INVALID
        if (!checked) {
            args.IsValid = false;
            return;
        }
    }

    // ✔ sab rows valid
    args.IsValid = true;
}
</script>



<script type="text/javascript">

    function ValidateHodRatings(src, args) {

        var table = document.getElementById("<%= Plandetails.ClientID %>");
        var rows = table.getElementsByTagName("tr");

        for (var i = 1; i < rows.length; i++)   // header छोड़कर
        {
            var checked = false;
            var radios = rows[i].getElementsByTagName("input");

            for (var j = 0; j < radios.length; j++) {

                if (radios[j].type == "radio" &&
                    radios[j].name.indexOf("HOD_Rating") >= 0 &&
                    radios[j].checked) {

                    checked = true;
                    break;
                }
            }

            if (!checked) {
                args.IsValid = false;   // ❌ इस row में कुछ select नहीं
                return;
            }
        }

        args.IsValid = true;  // ✔ सब rows valid
    }

</script>




<div class="form-group col-md-2 mb-1">
    <label class="m-0 mr-2 p-0 col-form-label-sm font-weight-bold fs-6">
        GIS Number:<span class="text-danger">*</span>
    </label>

    <asp:DropDownList ID="ddlGISNo" runat="server"
        CssClass="form-control form-control-sm col-12">
    </asp:DropDownList>

    <asp:RequiredFieldValidator
        ID="rfvGISNo"
        runat="server"
        ControlToValidate="ddlGISNo"
        InitialValue="0"
        ErrorMessage="Please select GIS Number"
        ValidationGroup="submit"
        ForeColor="Red" />
</div>

private async Task LoadGISDataAsync()
{
    string token = await GenerateTokenAsync();

    if (!string.IsNullOrEmpty(token))
    {
        string gisResponse = await GetGISDataAsync(token);

        DataSet filteredDS = CreateFilteredDataSet(gisResponse);

        ViewState["FilteredGISData"] = filteredDS;

        BindGISDropdown(filteredDS);
    }
}

private void BindGISDropdown(DataSet ds)
{
    if (ds != null && ds.Tables.Count > 0 && ds.Tables[0].Rows.Count > 0)
    {
        ddlGISNo.DataSource = ds.Tables[0];
        ddlGISNo.DataTextField = "RID";   // what user sees
        ddlGISNo.DataValueField = "RID";  // actual value
        ddlGISNo.DataBind();

        ddlGISNo.Items.Insert(0, new ListItem("-- Select GIS Number --", "0"));
    }
    else
    {
        ddlGISNo.Items.Clear();
        ddlGISNo.Items.Insert(0, new ListItem("No GIS Records Found", "0"));
    }
}


 
 
 <div class="form-group col-md-2 mb-1">
                                    <label for="GIS Number" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >GIS Number:<span class="text-danger">*</span></label>
                                    <asp:TextBox ID="GISNo" runat="server" CssClass="form-control form-control-sm col-12 input"  />
                                     <asp:CustomValidator ID="CustomValidator6" runat="server" ClientValidationFunction="ValidateWithZero" ValidationGroup="submit" ControlToValidate="GISNo" ValidateEmptyText="true"></asp:CustomValidator>
                                 </div>


protected async void Page_Load(object sender, EventArgs e)
        
        {
            Road_Records.DataSource = PageRecordsDataSet;
            Road_Record.DataSource = PageRecordDataSet;
            Checkpoint_Details.DataSource = PageRecordDataSet;
            if (!IsPostBack)
            {
                GetRecords(GetFilterCondition(), Road_Records.PageSize, 10, "");
                Road_Records.DataBind();
                Dictionary<string, object> ddlParam = new Dictionary<string, object>();
                ddlParam.Add("DivisionID", DBNull.Value);
                ddlParam.Add("DepartmentID", DBNull.Value);

                GetDropdowns("Division");
                GetDropdowns("Department", ddlParam);
                GetDropdowns("Section", ddlParam);

                div_details.Visible = false;

                PageRecordDataSet.Clear();
                //Road_Records.SelectedIndex = -1;
                PageRecordDataSet.EnforceConstraints = false;
                Road_Record.NewRecord();
                div_details.Visible = true;
                btnSave.Visible = true;


                         
                   await LoadGISDataAsync();

            }

        }


 private async Task LoadGISDataAsync()
        {
            string token = await GenerateTokenAsync();

            if (!string.IsNullOrEmpty(token))
            {
                string gisResponse = await GetGISDataAsync(token);

                DataSet filteredDS = CreateFilteredDataSet(gisResponse);

                ViewState["FilteredGISData"] = filteredDS;

            }

        }

        private DataSet CreateFilteredDataSet(string json)
        {
            DataSet ds = new DataSet();
            DataTable dt = new DataTable("FilteredData");

            dt.Columns.Add("RID");
            dt.Columns.Add("Status");
            dt.Columns.Add("BarricadingRequired");
           
            JObject obj = JObject.Parse(json);
            JArray features = (JArray)obj["features"];

            foreach (var feature in features)
            {
                JObject attr = (JObject)feature["attributes"];

                string barricading = attr["BarricadingRequired"]?.ToString();
                string status = attr["Status"]?.ToString();


                if (!string.Equals(barricading, "Yes", StringComparison.OrdinalIgnoreCase))
                    continue;



                var statusCodes = status.Split(',').Select(s => s.Trim()).ToList();

                if (statusCodes.Contains("16") && !statusCodes.Contains("19"))
                {
                    DataRow row = dt.NewRow();
                    row["RID"] = attr["RID"]?.ToString();

                    dt.Rows.Add(row);
                }
            }

            ds.Tables.Add(dt);
            return ds;
        }
