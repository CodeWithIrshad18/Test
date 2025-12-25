protected async void Road_Record_RowDataBound(object sender, GridViewRowEventArgs e)
{

    if (string.Equals(e.Row.RowType.ToString(), "DATAROW", StringComparison.InvariantCultureIgnoreCase))
    {
        DataSet ds1 = new DataSet();
        //DataRow oRow = ((DataRowView)e.Row.DataItem).Row;

        System.Web.UI.Control PNO = e.Row.FindControl("Pno");
        System.Web.UI.Control Name = e.Row.FindControl("Name");
        System.Web.UI.Control Department = e.Row.FindControl("Department");
        System.Web.UI.Control Designation = e.Row.FindControl("Designation");
        System.Web.UI.Control Level = e.Row.FindControl("Level");
        System.Web.UI.Control MailID = e.Row.FindControl("EmailID");

        ds1 = blglbl.GetEmployeeInfo(Session["UserName"].ToString());
        if (ds1.Tables[0].Rows.Count > 0)
        {
            ((TextBox)PNO).Text = ds1.Tables[0].Rows[0]["Pno"].ToString();
            ((TextBox)Name).Text = ds1.Tables[0].Rows[0]["EName"].ToString();
            ((TextBox)Department).Text = ds1.Tables[0].Rows[0]["DepartmentName"].ToString();
            ((TextBox)Designation).Text = ds1.Tables[0].Rows[0]["Designation"].ToString();
            ((TextBox)Level).Text = ds1.Tables[0].Rows[0]["Level"].ToString();
            ((TextBox)MailID).Text = ds1.Tables[0].Rows[0]["EmailID"].ToString();
        }

    }
    if (Road_Record.Rows.Count > 0)
    {
        BL_Road_side_barricade_Entry blobj = new BL_Road_side_barricade_Entry();
        DataSet ds = new DataSet();
        ds = blobj.GetCheckpoint();
        for (int i = 0; i < ds.Tables[0].Rows.Count; i++)
        {
            Checkpoint_Details.AddDataRow();
            PageRecordDataSet.Tables["App_Road_Side_Checkpoint_Details"].Rows[i]["SlNo"] = ds.Tables[0].Rows[i]["Slno"].ToString();
            PageRecordDataSet.Tables["App_Road_Side_Checkpoint_Details"].Rows[i]["Checkpoints"] = ds.Tables[0].Rows[i]["Checkpoints"].ToString();
        }


        await LoadGISDataAsync();
    }
        Checkpoint_Details.BindData();
        if (PageRecordDataSet.Tables["App_Road_side_barricade_Entry"].Rows[0]["Info_DivisionID"].ToString() != "" && PageRecordDataSet.Tables["App_Road_side_barricade_Entry"].Rows[0]["Info_DepartmentID"].ToString() != "" && PageRecordDataSet.Tables["App_Road_side_barricade_Entry"].Rows[0]["Info_SectionID"].ToString() != "")
        {
            if (e.Row.RowType.ToString().ToUpper() == "DATAROW")
            {
                DataRow oRow = ((DataRowView)e.Row.DataItem).Row;
                Dictionary<string, object> ddlParam = new Dictionary<string, object>();
                ddlParam.Add("DivisionID", oRow.ItemArray[8].ToString());
                GetDropdowns("Department", ddlParam);
                ((DropDownList)e.Row.FindControl("Info_DepartmentID")).DataBind();
                ((DropDownList)e.Row.FindControl("Info_DepartmentID")).SelectedValue = oRow.ItemArray[9].ToString();


            }
            if (e.Row.RowType.ToString().ToUpper() == "DATAROW")
            {
                DataRow oRow = ((DataRowView)e.Row.DataItem).Row;
                Dictionary<string, object> ddlParam = new Dictionary<string, object>();
                ddlParam.Add("DepartmentID", oRow.ItemArray[9].ToString());
                GetDropdowns("Section", ddlParam);
                ((DropDownList)e.Row.FindControl("Info_SectionID")).DataBind();
                ((DropDownList)e.Row.FindControl("Info_SectionID")).SelectedValue = oRow.ItemArray[10].ToString();
            }
        }
}



        private async Task LoadGISDataAsync()
        {
            string token = await GenerateTokenAsync();

            if (!string.IsNullOrEmpty(token))
            {
                string gisResponse = await GetGISDataAsync(token);

                DataSet filteredDS = CreateFilteredDataSet(gisResponse);

                


                BindGISDropdown(filteredDS);

            }

        }

        private void BindGISDropdown(DataSet ds)
        {
            DropDownList ddlGISNo = ((DropDownList)Road_Record.Rows[0].FindControl("GISNo"));
            if (ds != null && ds.Tables.Count > 0 && ds.Tables[0].Rows.Count > 0)
            {
                ddlGISNo.DataSource = ds.Tables[0];
                ddlGISNo.DataTextField = "RID";
                ddlGISNo.DataValueField = "RID";
                ddlGISNo.DataBind();

                ddlGISNo.Items.Insert(0, new ListItem("", "0"));
                ddlGISNo.SelectedIndex = 0;
            }
            else
            {
                ddlGISNo.Items.Clear();
                ddlGISNo.Items.Insert(0, new ListItem("No GIS Records Found", "0"));
            }
        }


        private DataSet CreateFilteredDataSet(string json)
        {
            DataSet ds = new DataSet();
            DataTable dt = new DataTable("FilteredData");

            dt.Columns.Add("RID");          
           
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



        private async Task<string> GenerateTokenAsync()
        {
            using (HttpClient client = new HttpClient())
            {
                client.Timeout = TimeSpan.FromSeconds(30);

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
