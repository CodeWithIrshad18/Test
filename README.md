private void BindDepartmentDropdown(string division)
{
    string query = @"
        SELECT DISTINCT ema_dept_desc 
        FROM SAPHRDB.dbo.T_Empl_All
        WHERE ema_dept_desc IS NOT NULL
          AND ema_exec_head_desc = '" + division.Replace("'", "''") + @"'
        ORDER BY ema_dept_desc";

    DataTable dt = GetData(query);

    DeptDropdown.DataSource = dt;
    DeptDropdown.DataTextField = "ema_dept_desc";
    DeptDropdown.DataValueField = "ema_dept_desc";
    DeptDropdown.DataBind();

    DeptDropdown.Items.Insert(0, new ListItem("-- Select Department --", ""));
}






<asp:DropDownList 
    ID="DivDropdown" 
    runat="server" 
    CssClass="form-control form-control-sm col-sm-8"
    AutoPostBack="true"
    OnSelectedIndexChanged="DivDropdown_SelectedIndexChanged">
    <asp:ListItem Text="-- Select Division --" Value="" />
</asp:DropDownList>

                                       <asp:DropDownList 
    ID="DeptDropdown" 
    runat="server" 
    CssClass="form-control form-control-sm col-sm-8">
    <asp:ListItem Text="-- Select Department --" Value="" />
</asp:DropDownList>
                                

private void BindDepartmentDropdown(string division)
{
    string query = @"
        SELECT DISTINCT ema_dept_desc 
        FROM SAPHRDB.dbo.T_Empl_All
        WHERE ema_dept_desc IS NOT NULL
          AND ema_exec_head_desc = @Division
        ORDER BY ema_dept_desc";

    SqlParameter[] param = {
        new SqlParameter("@Division", division)
    };

    DataTable dt = GetData(query, param);

    DeptDropdown.DataSource = dt;
    DeptDropdown.DataTextField = "ema_dept_desc";
    DeptDropdown.DataValueField = "ema_dept_desc";
    DeptDropdown.DataBind();

    DeptDropdown.Items.Insert(0, new ListItem("-- Select Department --", ""));
}

protected void DivDropdown_SelectedIndexChanged(object sender, EventArgs e)
{
    string selectedDivision = DivDropdown.SelectedValue;

    DeptDropdown.Items.Clear();
    DeptDropdown.Items.Insert(0, new ListItem("-- Select Department --", ""));

    if (!string.IsNullOrEmpty(selectedDivision))
    {
        BindDepartmentDropdown(selectedDivision);
    }
}
                                                                       
                                                                       <div class="form-group col-md-3 mb-1">
<label for="Division" class="m-0 mr-2 p-0 col-form-label-sm col-sm-3 font-weight-bold fs-6">Division:</label>
<asp:DropDownList ID="DivDropdown" runat="server" CssClass="form-control form-control-sm col-sm-8" AutoPostBack="false">
    <asp:ListItem Text="-- Select Division --" Value="" />
</asp:DropDownList>
                                 </div>

                                 <div class="form-group col-md-3 mb-1">
    <label for="Department" class="m-0 mr-2 p-0 col-form-label-sm col-sm-3 font-weight-bold fs-6">Department:</label>
    <asp:DropDownList ID="DeptDropdown" runat="server" CssClass="form-control form-control-sm col-sm-8" AutoPostBack="false">
        <asp:ListItem Text="-- Select Department --" Value="" />
    </asp:DropDownList>
                                     </div>


       private void BindDepartmentDropdown()
       {
           string query = "SELECT DISTINCT ema_dept_desc FROM SAPHRDB.dbo.T_Empl_All  where ema_dept_desc is not Null order by ema_dept_desc";
           DataTable dt = GetData(query);
           DeptDropdown.DataSource = dt;
           DeptDropdown.DataTextField = "ema_dept_desc";
           DeptDropdown.DataValueField = "ema_dept_desc";
           DeptDropdown.DataBind();
           DeptDropdown.Items.Insert(0, new ListItem("-- Select Department --", ""));
       }

       private void BindDivisionDropdown()
       {
           string query = "SELECT DISTINCT ema_exec_head_desc FROM SAPHRDB.dbo.T_Empl_All  where ema_exec_head_desc is not Null order by ema_exec_head_desc";
           DataTable dt = GetData(query);
           DivDropdown.DataSource = dt;
           DivDropdown.DataTextField = "ema_exec_head_desc";
           DivDropdown.DataValueField = "ema_exec_head_desc";
           DivDropdown.DataBind();
           DivDropdown.Items.Insert(0, new ListItem("-- Select Division --", ""));
       }

  protected void Page_Load(object sender, EventArgs e)
  {
      if (!IsPostBack)
      {
          string today = DateTime.Now.ToString("yyyy/MM/dd");
          fromdate.Text = today;
          todate.Text = today;
          ReportViewer1.Visible = false;
          BindDepartmentDropdown();
          BindDivisionDropdown();
      }
      else
      {
          string eventTarget = Request.Params["__EVENTTARGET"];


          if (!string.IsNullOrEmpty(eventTarget) && !eventTarget.Contains("ReportViewer1"))
          {
              if (ViewState["from"] != null && ViewState["to"] != null)
              {
                  string from = ViewState["from"].ToString();
                  string to = ViewState["to"].ToString();
                  string dept = ViewState["dept"]?.ToString();
                  string Div = ViewState["Div"]?.ToString();
                  string type = ViewState["type"]?.ToString();
                  string attempt = ViewState["attempt"]?.ToString();
                  string employeeType = ViewState["employeeType"]?.ToString();

                  LoadReport(from, to, dept, type, attempt, employeeType,Div);
              }
          }
      }
  }
