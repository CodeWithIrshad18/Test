private DataSet CreateFilteredDataSet(string json)
{
    DataSet ds = new DataSet();
    DataTable dt = new DataTable("FilteredData");

    dt.Columns.Add("RID");
    dt.Columns.Add("Status");
    dt.Columns.Add("BarricadingRequired");
    dt.Columns.Add("department");
    dt.Columns.Add("name");
    dt.Columns.Add("mobile");
    dt.Columns.Add("OBJECTID");

    JObject obj = JObject.Parse(json);
    JArray features = (JArray)obj["features"];

    foreach (var feature in features)
    {
        JObject attr = (JObject)feature["attributes"];

        string barricading = attr["BarricadingRequired"]?.ToString();
        string status = attr["Status"]?.ToString();

        // 🔴 RULE 1: BarricadingRequired must be Yes
        if (!string.Equals(barricading, "Yes", StringComparison.OrdinalIgnoreCase))
            continue;

        // 🔴 RULE 2: Status logic
        var statusCodes = status.Split(',').Select(s => s.Trim()).ToList();

        if (statusCodes.Contains("16") && !statusCodes.Contains("19"))
        {
            DataRow row = dt.NewRow();
            row["RID"] = attr["RID"]?.ToString();
            row["Status"] = status;
            row["BarricadingRequired"] = barricading;
            row["department"] = attr["department"]?.ToString();
            row["name"] = attr["name"]?.ToString();
            row["mobile"] = attr["mobile"]?.ToString();
            row["OBJECTID"] = attr["OBJECTID"]?.ToString();

            dt.Rows.Add(row);
        }
    }

    ds.Tables.Add(dt);
    return ds;
}
private async Task LoadGISDataAsync()
{
    string token = await GenerateTokenAsync();

    if (!string.IsNullOrEmpty(token))
    {
        string gisResponse = await GetGISDataAsync(token);

        DataSet filteredDS = CreateFilteredDataSet(gisResponse);

        // Store anywhere you need
        ViewState["FilteredGISData"] = filteredDS;

        // Example: bind to grid
        // GridView1.DataSource = filteredDS.Tables[0];
        // GridView1.DataBind();
    }
}

 
 
 
 "features": [
        {
            "attributes": {
                "RID": "WMD25A00082",
                "Status": "2,12,8,4,10,6,16,18,19",
                "requestType": "new",
                "oldRequestNo": null,
                "jobStartDate": 1735948800000,
                "jobEndDate": 1741046400000,
                "pNo": "128772",
                "name": "Sanjay Kumar",
                "mobile": "8825233402",
                "department": "WMD",
                "clearanceRequestFor": "Trench cutting in road flank/road side excavation",
                "excavationPurpose": "Repairing of water leakages",
                "excavationType": "Concrete Breaking",
                "safetyOfficer": "155503",
                "ioNumber": "0WTR00161205",
                "workOrderNumber": "4700024810",
                "workOrderItemNumber": null,
                "employeePNo": "842044",
                "employeeName": "Avinash Yadav",
                "employeeMobileNo": "6287099783",
                "vendorPNo": "14219",
                "vendorName": "Pankaj kumar",
                "vendorMobileNo": "7209503750",
                "CreatedBy": "128772",
                "CreatedOn": 1735908469000,
                "UpdatedBy": "842064",
                "UpdatedOn": 1736754086000,
                "excavationPurposeOtherInfo": null,
                "extensionRemarks": null,
                "GlobalID": "{8B68D1B6-E789-43CB-8C6F-144C3FDAAE65}",
                "created_user": "APP_CITY",
                "created_date": 1763524580000,
                "last_edited_user": "APP_CITY",
                "last_edited_date": 1763524580000,
                "OBJECTID": 1754,
                "BarricadingRequired": null
            }
        },
       {
            "attributes": {
                "RID": "Information Technology25A03785",
                "Status": "2,4,6,10,8,13,16,18",
                "requestType": "new",
                "oldRequestNo": null,
                "jobStartDate": 1766966400000,
                "jobEndDate": 1767225600000,
                "pNo": "159445",
                "name": "Adwine",
                "mobile": "6287090147",
                "department": "Information Technology",
                "clearanceRequestFor": "Trench cutting in road flank/road side excavation",
                "excavationPurpose": "Repairing of cables",
                "excavationType": "Concrete Breaking",
                "safetyOfficer": "155237",
                "ioNumber": "4523453534534",
                "workOrderNumber": "8765782638",
                "workOrderItemNumber": "98473984759",
                "employeePNo": "7457543",
                "employeeName": "TestingEmp5",
                "employeeMobileNo": "6353456345",
                "vendorPNo": "4737845",
                "vendorName": "VendorEmp5",
                "vendorMobileNo": "3984759834",
                "CreatedBy": "159445",
                "CreatedOn": 1766556394000,
                "UpdatedBy": "159608",
                "UpdatedOn": 1766557088000,
                "excavationPurposeOtherInfo": null,
                "extensionRemarks": null,
                "GlobalID": "{EBA1D2C7-8E7B-47F8-880D-E879F63C996B}",
                "created_user": "Esri_Anonymous",
                "created_date": 1766556394000,
                "last_edited_user": "Esri_Anonymous",
                "last_edited_date": 1766557088000,
                "OBJECTID": 4899,
                "BarricadingRequired": "Yes"
            }
        }
    ]
