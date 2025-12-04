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
