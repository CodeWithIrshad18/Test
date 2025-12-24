        protected  void Page_Load(object sender, EventArgs e)
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


                HttpClient client = new HttpClient();              
                    LoadGISDataAsync();

            }

        }

        private async Task LoadGISDataAsync()
        {
            string token = await GenerateTokenAsync();

            if (!string.IsNullOrEmpty(token))
            {
                string gisResponse = await GetGISDataAsync(token);
                ViewState["GIS_RESPONSE"] = gisResponse;
            }
        }


        private async Task<string> GenerateTokenAsync()
        {
            using (HttpClient client = new HttpClient())
            {
                //client.Timeout = TimeSpan.FromSeconds(30);

                var formData = new FormUrlEncodedContent(new[]
                {
            new KeyValuePair<string, string>("f", "pjson"),
            new KeyValuePair<string, string>("username", "adwine.jha"),
            new KeyValuePair<string, string>("password", "aDwine@oth25"),
            new KeyValuePair<string, string>("referer", "https://uonegis.tatasteel.co.in")
        });

                var response = await client.PostAsync(
                    "https://uonegis.tatasteel.co.in/portal/sharing/rest/generateToken",
                    formData
                ).ConfigureAwait(false);

                string json = await response.Content.ReadAsStringAsync();
                return JObject.Parse(json)["token"]?.ToString();
            }
        }


        private async Task<string> GetGISDataAsync(string token)
{
    using (HttpClient client = new HttpClient())
    {
        var url = "https://uonegis.tatasteel.co.in/agshostsrvr/rest/services/CITYGIS/GISCL_Integration/MapServer/2/query";

        var formData = new FormUrlEncodedContent(new[]
        {
            new KeyValuePair<string, string>("where", "1=1"),
            new KeyValuePair<string, string>("f", "pjson"),
            new KeyValuePair<string, string>("outFields", "*"),
            new KeyValuePair<string, string>("token", token)
        });

        var response = await client.PostAsync(url, formData);
        return await response.Content.ReadAsStringAsync();
    }
}
