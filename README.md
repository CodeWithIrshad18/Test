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
