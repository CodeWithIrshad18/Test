private void BindReport()
{
    DataTable dt = new DataTable();

    string conStr = System.Configuration.ConfigurationManager
                        .ConnectionStrings["YourConnectionString"].ConnectionString;

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
    ReportViewer1.LocalReport.ReportPath = Server.MapPath("~/ModuleReport.rdlc");

    ReportDataSource rds = new ReportDataSource("QuizResultDS", dt);

    ReportViewer1.LocalReport.DataSources.Clear();
    ReportViewer1.LocalReport.DataSources.Add(rds);

    ReportViewer1.LocalReport.Refresh();
    ReportViewer1.Visible = true;
}





<%@ Page Title="" Language="C#" MasterPageFile="~/Site.Master" AutoEventWireup="true" CodeBehind="ModuleReport.aspx.cs" Inherits="POS.App.Input.ModuleReport" %>
<%@ Register TagPrefix="rsweb" Namespace="Microsoft.Reporting.WebForms" Assembly="Microsoft.ReportViewer.WebForms" %>
<asp:Content ID="Content1" ContentPlaceHolderID="HeadContent" runat="server">
</asp:Content>
<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">

   
    <div class="mt-4 form-group col-md-2">
           <asp:Button ID="btnSearch" runat="server" Text="Search" CssClass="btn btn-success btn-sm" OnClick="btnSearch_Click"/>
            </div>


        <asp:ScriptManager ID="ScriptManager1" runat="server" />

        <rsweb:ReportViewer ID="ReportViewer1" runat="server" Width="100%" Height="600px">
            <LocalReport ReportPath="ModuleReport.rdlc">
            </LocalReport>
        </rsweb:ReportViewer>



</asp:Content>


protected void Page_Load(object sender, EventArgs e)
{

}

protected void btnSearch_Click(object sender, EventArgs e)
{
   

}



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
ORDER BY UR.Answered_On;
