if (!IsPostBack)
{
    GetDropdowns("PnoDropdown");
    PnoDropdown.DataBind();

    PnoDropdown.Items.Insert(0, new ListItem("-- All P.No --", ""));
    ReportViewer1.Visible = false;
}

private void BindReport()
{
    DataTable dt = new DataTable();

    string conStr = ConfigurationManager
                        .ConnectionStrings["connect"].ConnectionString;

    using (SqlConnection con = new SqlConnection(conStr))
    {
        using (SqlCommand cmd = new SqlCommand(@"
            SELECT  
                UR.UserID,
                MS.ModuleName,
                QM.QuestionType,
                QM.Question,

                CASE 
                    WHEN QM.QuestionType = 'Objective' THEN
                        CASE QM.Ans
                            WHEN 1 THEN QM.Option1
                            WHEN 2 THEN QM.Option2
                            WHEN 3 THEN QM.Option3
                            WHEN 4 THEN QM.Option4
                        END
                    ELSE 'Subjective'
                END AS CorrectAnswer,

                CASE 
                    WHEN QM.QuestionType = 'Objective' THEN
                        CASE UR.SelectedOption
                            WHEN 1 THEN QM.Option1
                            WHEN 2 THEN QM.Option2
                            WHEN 3 THEN QM.Option3
                            WHEN 4 THEN QM.Option4
                        END
                    ELSE UR.Subjective_Answer
                END AS UserAnswer,

                UR.IsCorrect,
                UR.Answered_On

            FROM ASP_User_Response UR
            INNER JOIN App_QuestionMaster QM 
                ON QM.ID = UR.QuestionID
            INNER JOIN App_Module_Master MS 
                ON MS.ID = UR.ModuleID
            WHERE QM.IsActive = 1
              AND (@UserID IS NULL OR UR.UserID = @UserID)
            ORDER BY UR.Answered_On
        ", con))
        {
            // 👇 ONLY apply filter when value selected
            if (string.IsNullOrEmpty(PnoDropdown.SelectedValue))
                cmd.Parameters.Add("@UserID", SqlDbType.Int).Value = DBNull.Value;
            else
                cmd.Parameters.Add("@UserID", SqlDbType.Int)
                               .Value = Convert.ToInt32(PnoDropdown.SelectedValue);

            using (SqlDataAdapter da = new SqlDataAdapter(cmd))
            {
                da.Fill(dt);
            }
        }
    }

    ReportViewer1.Reset();
    ReportViewer1.LocalReport.ReportPath = Server.MapPath("ModuleReport.rdlc");

    ReportDataSource rds = new ReportDataSource("DataSet1", dt);
    ReportViewer1.LocalReport.DataSources.Clear();
    ReportViewer1.LocalReport.DataSources.Add(rds);

    ReportViewer1.LocalReport.Refresh();
    ReportViewer1.Visible = true;
}






private void BindReport()
{
    DataTable dt = new DataTable();

    string conStr = System.Configuration.ConfigurationManager
                        .ConnectionStrings["connect"].ConnectionString;

    using (SqlConnection con = new SqlConnection(conStr))
    {
        using (SqlCommand cmd = new SqlCommand(@"
            SELECT  
                UR.UserID,
                MS.ModuleName,
                QM.QuestionType,
                QM.Question,

                CASE 
                    WHEN QM.QuestionType = 'Objective' THEN
                        CASE QM.Ans
                            WHEN 1 THEN QM.Option1
                            WHEN 2 THEN QM.Option2
                            WHEN 3 THEN QM.Option3
                            WHEN 4 THEN QM.Option4
                        END
                    ELSE 'Subjective'
                END AS CorrectAnswer,

                CASE 
                    WHEN QM.QuestionType = 'Objective' THEN
                        CASE UR.SelectedOption
                            WHEN 1 THEN QM.Option1
                            WHEN 2 THEN QM.Option2
                            WHEN 3 THEN QM.Option3
                            WHEN 4 THEN QM.Option4
                        END
                    ELSE UR.Subjective_Answer
                END AS UserAnswer,

                UR.IsCorrect,
                UR.Answered_On

            FROM ASP_User_Response UR
            INNER JOIN App_QuestionMaster QM 
                ON QM.ID = UR.QuestionID
            INNER JOIN App_Module_Master MS 
                ON MS.ID = UR.ModuleID
            WHERE QM.IsActive = 1
              AND UR.UserID = @UserID
            ORDER BY UR.Answered_On
        ", con))
        {
            // 🔹 Pass dropdown value
            cmd.Parameters.Add("@UserID", SqlDbType.Int)
                           .Value = Convert.ToInt32(PnoDropdown.SelectedValue);

            using (SqlDataAdapter da = new SqlDataAdapter(cmd))
            {
                da.Fill(dt);
            }
        }
    }

    ReportViewer1.Reset();
    ReportViewer1.LocalReport.ReportPath = Server.MapPath("ModuleReport.rdlc");

    // ⚠ Dataset name must match RDLC
    ReportDataSource rds = new ReportDataSource("DataSet1", dt);

    ReportViewer1.LocalReport.DataSources.Clear();
    ReportViewer1.LocalReport.DataSources.Add(rds);

    ReportViewer1.LocalReport.Refresh();
    ReportViewer1.Visible = true;
}






<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">

   
   
    <fieldset class="" style="border:1px solid #bfbebe;padding:5px 20px 5px 20px;border-radius:6px">
        <legend style="width:auto;border:0;font-size:14px;margin:0px 6px 0px 6px;padding:0px 5px 0px 5px;color:#0000FF"><b>Search</b></legend>
             <div class="form-inline row">
         <div class="form-group col-md-4 mb-1">
      <label for="txtDepartmentSearch" class="m-0 mr-2 p-0 col-form-label-sm col-sm-3 font-weight-bold fs-6" >P.No.:</label>
      <asp:DropDownList ID="PnoDropdown" runat="server" DataSource="<%# PageDDLDataset %>" DataMember="PnoDropdown" DataValueField="Pno" DataTextField="Pno" CssClass="form-control form-control-sm col-sm-8" />
     
  </div>
     <div class="form-group col-md-2">
           <asp:Button ID="btnSearch" runat="server" Text="Search" CssClass="btn btn-success btn-sm" OnClick="btnSearch_Click"/>
            </div>
</div>
        </fieldset>

        <rsweb:ReportViewer ID="ReportViewer1" runat="server" Width="100%" Height="600px">
            <LocalReport ReportPath="ModuleReport.rdlc">
            </LocalReport>
        </rsweb:ReportViewer>



</asp:Content>



  protected void Page_Load(object sender, EventArgs e)
  {
      if (!IsPostBack)
      {

          GetDropdowns("PnoDropdown");
          PnoDropdown.DataBind();
          ReportViewer1.Visible = false;

      }
  }

  protected void btnSearch_Click(object sender, EventArgs e)
  {

      BindReport();
  }


  private void BindReport()
  {
      DataTable dt = new DataTable();

      string conStr = System.Configuration.ConfigurationManager
                          .ConnectionStrings["connect"].ConnectionString;

      using (SqlConnection con = new SqlConnection(conStr))
      {
          using (SqlCommand cmd = new SqlCommand(@"
      SELECT  
          UR.UserID,
          MS.ModuleName,
          QM.QuestionType,
          QM.Question,

          CASE 
              WHEN QM.QuestionType = 'Objective' THEN
                  CASE QM.Ans
                      WHEN 1 THEN QM.Option1
                      WHEN 2 THEN QM.Option2
                      WHEN 3 THEN QM.Option3
                      WHEN 4 THEN QM.Option4
                  END
              ELSE 'Subjective'
          END AS CorrectAnswer,

          CASE 
              WHEN QM.QuestionType = 'Objective' THEN
                  CASE UR.SelectedOption
                      WHEN 1 THEN QM.Option1
                      WHEN 2 THEN QM.Option2
                      WHEN 3 THEN QM.Option3
                      WHEN 4 THEN QM.Option4
                  END
              ELSE UR.Subjective_Answer
          END AS UserAnswer,

          UR.IsCorrect,
          UR.Answered_On

      FROM ASP_User_Response UR
      INNER JOIN App_QuestionMaster QM 
          ON QM.ID = UR.QuestionID
      INNER JOIN App_Module_Master MS 
          ON MS.ID = UR.ModuleID
      WHERE QM.IsActive = 1
      ORDER BY UR.Answered_On
  ", con))
          {
              using (SqlDataAdapter da = new SqlDataAdapter(cmd))
              {
                  da.Fill(dt);
              }
          }
      }

      ReportViewer1.Reset();
      ReportViewer1.LocalReport.ReportPath = Server.MapPath("ModuleReport.rdlc");

      ReportDataSource rds = new ReportDataSource("DataSet1", dt);

      ReportViewer1.LocalReport.DataSources.Clear();
      ReportViewer1.LocalReport.DataSources.Add(rds);

      ReportViewer1.LocalReport.Refresh();
      ReportViewer1.Visible = true;
  }
