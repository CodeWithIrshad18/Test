if (Session["UserId"] != null && Session["UserName"] != null)
{
    Response.Write("<span style='color:green'>Session stored successfully!</span><br/>");
    Response.Write("UserId = " + Session["UserId"] + "<br/>");
    Response.Write("UserName = " + Session["UserName"] + "<br/>");
}
else
{
    Response.Write("<span style='color:red'>Session NOT stored!</span><br/>");
}




protected void Page_Load(object sender, EventArgs e)
{
    // If session not yet created
    if (Session["UserName"] == null)
    {
        Session["UserName"] = "";
        Session["PasswordQuestion"] = "";
        return;   // ⬅ important
    }

    string userName = Session["UserName"].ToString();

    if (string.IsNullOrWhiteSpace(userName))
    {
        Session["PasswordQuestion"] = "";
        return;
    }

    // SAFE membership lookup
    var user = Membership.GetUser(userName);

    if (user == null)
    {
        Session["PasswordQuestion"] = "";
        return;
    }

    // Valid user
    Session["PasswordQuestion"] = user.PasswordQuestion;


    // ---------------- Your existing DB code ----------------
    DataSet ds = new DataSet();
    string strSQL = "select App_ApplicationFormName, AllowRead, AllowWrite, AllowDelete, AllowAll " +
                    "from App_ApplicationForms left join App_UserFormPermission on Id=formid " +
                    "left join aspnet_Users on App_UserFormPermission.UserId = aspnet_Users.UserId " +
                    "where App_ApplicationFormName in ('UserPermission','DepartmentMaster') " +
                    "and UserName = @userName";

    Dictionary<string, object> objParam = new Dictionary<string, object>();
    objParam.Add("userName", userName);

    DataHelper dh = new DataHelper();
    ds = dh.GetDataset(strSQL, "App_UserFormPermission", objParam);
    Session["initial"] = "true";
}

  
  
  protected void Page_Load(object sender, EventArgs e)
  {

      if (Session["UserName"] == null)
      {
          Session["UserName"] = "";
          Session["PasswordQuestion"] = "";
      }
      else
      {
          if(Session["UserName"].ToString() == "" )
              Session["PasswordQuestion"] = "";
          else
              Session["PasswordQuestion"] = Membership.GetUser(Session["UserName"].ToString()).PasswordQuestion;
      }
      
          
          DataSet ds = new DataSet();
          string strSQL = "select  App_ApplicationFormName,AllowRead, AllowWrite, AllowDelete,AllowAll from App_ApplicationForms left join App_UserFormPermission on Id=formid  left join aspnet_Users on App_UserFormPermission.UserId= aspnet_Users.UserId where App_ApplicationFormName in ('UserPermission','DepartmentMaster') and UserName = @userName";
          Dictionary<string, object> objParam = new Dictionary<string, object>();
          objParam.Add("userName", Session["UserName"].ToString());
          DataHelper dh = new DataHelper();
          ds = dh.GetDataset(strSQL, "App_UserFormPermission", objParam);
          Session["initial"] = "true";
          if (ds.Tables[0].Rows.Count>0)
          {
              //if (ds.Tables[0].Rows[0].ItemArray[1].ToString() == "False")
              //{
              //    NavigationMenu.Items.Remove(NavigationMenu.FindItem("Admin"));

              //}
              //if (ds.Tables[0].Rows[1].ItemArray[1].ToString() == "False")
              //    NavigationMenu.Items.Remove(NavigationMenu.FindItem("Master Data"));
          }   
  }
