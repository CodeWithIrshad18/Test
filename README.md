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
