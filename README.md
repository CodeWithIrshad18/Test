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




protected void Page_Load(object sender, EventArgs e)
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
        PageRecordDataSet.EnforceConstraints = false;
        Road_Record.NewRecord();
        div_details.Visible = true;
        btnSave.Visible = true;

        // 🔴 CALL GIS APIs
        LoadGISData();
    }
}

private void LoadGISData()
{
    try
    {
        string token = GenerateToken().Result;

        if (!string.IsNullOrEmpty(token))
        {
            string gisResponse = GetGISData(token).Result;

            // You get FULL response here (JSON)
            // Store or process as required
            ViewState["GIS_RESPONSE"] = gisResponse;

            // Example: Debug / log
            // Response.Write(gisResponse);
        }
    }
    catch (Exception ex)
    {
        // Log error
    }
}

private async Task<string> GenerateToken()
{
    using (HttpClient client = new HttpClient())
    {
        var url = "https://uonegis.tatasteel.co.in/portal/sharing/rest/generateToken";

        var formData = new FormUrlEncodedContent(new[]
        {
            new KeyValuePair<string, string>("f", "pjson"),
            new KeyValuePair<string, string>("username", "adwine.jha"),
            new KeyValuePair<string, string>("password", "aDwine@oth25"),
            new KeyValuePair<string, string>("referer", "https://uonegis.tatasteel.co.in")
        });

        var response = await client.PostAsync(url, formData);
        string json = await response.Content.ReadAsStringAsync();

        JObject obj = JObject.Parse(json);
        return obj["token"]?.ToString();
    }
}
 
private async Task<string> GetGISData(string token)
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
using System.Net.Http;
using System.Threading.Tasks;
using Newtonsoft.Json.Linq;
using System.Text;



 
 
 protected void Page_Load(object sender, EventArgs e)
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


     }

 }


1.for token generation

https://uonegis.tatasteel.co.in/portal/sharing/rest/generateToken

form data in body

f pjson
username adwine.jha
password aDwine@oth25
referer https://uonegis.tatasteel.co.in


2.for Response Data 

https://uonegis.tatasteel.co.in/agshostsrvr/rest/services/CITYGIS/GISCL_Integration/MapServer/2/query

form data in body
Key    value
where   1=1
f       pjson
outFields  *
token   Z0SkFgsZHI1ZePjruHD6-JOzMJ6iSrin37BoJq-BkXoVqoey6Bd-lk_58m-s2-aN8OA8KBn-v_YsNgqnk6urOQW5WvCP9jkCkXEAS2BvdOMsLaz267jMbMeRKDUL8DumgAFRmvDTsVGqVnxNhzL7W6tk3oxeOvc2SizFQe-u1eTRSLkKpjEHUcTxyNylzKDd


i want to get all the response in pageload 
