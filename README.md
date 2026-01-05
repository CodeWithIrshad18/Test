using System;
using System.Data;
using System.Data.SqlClient;
using System.Configuration;

namespace POS.App.Input
{
    public partial class Quiz : System.Web.UI.Page
    {
        string cs = ConfigurationManager.ConnectionStrings["ApplicationServices"].ConnectionString;

        protected void Page_Load(object sender, EventArgs e)
        {
            if (!IsPostBack)
                LoadSlides();
        }

        void LoadSlides()
        {
            DataTable dtSlides = new DataTable();
            dtSlides.Columns.Add("SlideType");
            dtSlides.Columns.Add("ModuleId");
            dtSlides.Columns.Add("ModuleName");
            dtSlides.Columns.Add("ModuleImage");
            dtSlides.Columns.Add("QuestionCount");
            dtSlides.Columns.Add("Question");
            dtSlides.Columns.Add("QuestionType");
            dtSlides.Columns.Add("Option1");
            dtSlides.Columns.Add("Option2");
            dtSlides.Columns.Add("Option3");
            dtSlides.Columns.Add("Option4");
            dtSlides.Columns.Add("QuestionImage");

            string sql = @"
                SELECT 
                    M.ID AS ModuleId,
                    M.ModuleName,
                    A.Attachments AS ModuleImage,
                    Q.Id AS QuestionId,
                    Q.QuestionType,
                    Q.Question,
                    Q.Option1,
                    Q.Option2,
                    Q.Option3,
                    Q.Option4,
                    Q.QuestionImage,
                    Q.SeqNo
                FROM App_Module_Master M
                LEFT JOIN App_Module_Attachments A
                    ON A.ModuleID = M.ID AND A.SeqNo = 1 AND A.IsActive = 1
                LEFT JOIN App_QuestionMaster Q
                    ON Q.ModuleID = M.ID AND Q.IsActive = 1
                WHERE M.IsActive = 1
                ORDER BY M.SerialNo, Q.SeqNo";

            using (SqlConnection con = new SqlConnection(cs))
            using (SqlCommand cmd = new SqlCommand(sql, con))
            {
                con.Open();
                SqlDataReader dr = cmd.ExecuteReader();

                string lastModuleId = "";

                while (dr.Read())
                {
                    string moduleId = dr["ModuleId"].ToString();

                    // MODULE SLIDE
                    if (lastModuleId != moduleId)
                    {
                        DataRow m = dtSlides.NewRow();
                        m["SlideType"] = "MODULE";
                        m["ModuleId"] = moduleId;
                        m["ModuleName"] = dr["ModuleName"];
                        m["ModuleImage"] = dr["ModuleImage"] == DBNull.Value ? "" : dr["ModuleImage"];
                        m["QuestionCount"] = GetQuestionCount(moduleId);
                        dtSlides.Rows.Add(m);

                        lastModuleId = moduleId;
                    }

                    // QUESTION SLIDE
                    if (dr["QuestionId"] != DBNull.Value)
                    {
                        DataRow q = dtSlides.NewRow();
                        q["SlideType"] = "QUESTION";
                        q["ModuleId"] = moduleId;
                        q["ModuleName"] = dr["ModuleName"];
                        q["Question"] = dr["Question"];
                        q["QuestionType"] = dr["QuestionType"];
                        q["Option1"] = dr["Option1"];
                        q["Option2"] = dr["Option2"];
                        q["Option3"] = dr["Option3"];
                        q["Option4"] = dr["Option4"];
                        q["QuestionImage"] = dr["QuestionImage"];
                        dtSlides.Rows.Add(q);
                    }
                }
            }

            rptSlides.DataSource = dtSlides;
            rptSlides.DataBind();
        }

        // ✅ FULL METHOD – NO ERROR NOW
        int GetQuestionCount(string moduleId)
        {
            using (SqlConnection con = new SqlConnection(cs))
            using (SqlCommand cmd = new SqlCommand(
                "SELECT COUNT(*) FROM App_QuestionMaster WHERE ModuleID = @ModuleId AND IsActive = 1", con))
            {
                cmd.Parameters.AddWithValue("@ModuleId", moduleId);
                con.Open();
                return Convert.ToInt32(cmd.ExecuteScalar());
            }
        }
    }
}


<%@ Page Language="C#" AutoEventWireup="true" CodeBehind="Quiz.aspx.cs"
    Inherits="POS.App.Input.Quiz" %>

<!DOCTYPE html>
<html>
<head runat="server">
    <title>Quiz</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet" />
</head>
<body>

<form id="form1" runat="server">

<div class="container mt-4">

    <div id="quizCarousel" class="carousel slide" data-bs-interval="false">
        <div class="carousel-inner">

            <asp:Repeater ID="rptSlides" runat="server">
                <ItemTemplate>

                    <div class='carousel-item <%# Container.ItemIndex == 0 ? "active" : "" %>'>

                        <!-- MODULE SLIDE -->
                        <asp:Panel runat="server"
                            Visible='<%# Eval("SlideType").ToString() == "MODULE" %>'>
                            <div class="card text-center shadow p-4">

                                <asp:Image runat="server"
                                    ImageUrl='<%# string.IsNullOrEmpty(Eval("ModuleImage").ToString())
                                        ? ResolveUrl("~/Images/no-image.png")
                                        : ResolveUrl("~/Upload/" + Eval("ModuleImage")) %>'
                                    CssClass="img-fluid mb-3"
                                    Style="height:260px; object-fit:cover;" />

                                <h3><%# Eval("ModuleName") %></h3>

                                <p class="text-muted">
                                    <%# Eval("QuestionCount") %> Questions
                                </p>

                            </div>
                        </asp:Panel>

                        <!-- QUESTION SLIDE -->
                        <asp:Panel runat="server"
                            Visible='<%# Eval("SlideType").ToString() == "QUESTION" %>'>
                            <div class="card shadow p-4">

                                <h6 class="text-muted">
                                    <%# Eval("ModuleName") %>
                                </h6>

                                <h5 class="mb-3">
                                    <%# Eval("Question") %>
                                </h5>

                                <!-- QUESTION IMAGE -->
                                <asp:Image runat="server"
                                    Visible='<%# Eval("QuestionImage") != DBNull.Value %>'
                                    ImageUrl='<%# ResolveUrl("~/Upload/" + Eval("QuestionImage")) %>'
                                    CssClass="img-fluid mb-3" />

                                <!-- OBJECTIVE -->
                                <asp:RadioButtonList runat="server"
                                    Visible='<%# Eval("QuestionType").ToString() == "Objective" %>'
                                    CssClass="list-group">
                                    <asp:ListItem Text='<%# Eval("Option1") %>' Value="1" />
                                    <asp:ListItem Text='<%# Eval("Option2") %>' Value="2" />
                                    <asp:ListItem Text='<%# Eval("Option3") %>' Value="3" />
                                    <asp:ListItem Text='<%# Eval("Option4") %>' Value="4" />
                                </asp:RadioButtonList>

                                <!-- SUBJECTIVE -->
                                <asp:TextBox runat="server"
                                    Visible='<%# Eval("QuestionType").ToString() == "Subjective" %>'
                                    CssClass="form-control"
                                    TextMode="MultiLine"
                                    Rows="3" />

                            </div>
                        </asp:Panel>

                    </div>

                </ItemTemplate>
            </asp:Repeater>

        </div>

        <!-- Controls -->
        <button class="carousel-control-prev" type="button"
            data-bs-target="#quizCarousel" data-bs-slide="prev">
            <span class="carousel-control-prev-icon"></span>
        </button>

        <button class="carousel-control-next" type="button"
            data-bs-target="#quizCarousel" data-bs-slide="next">
            <span class="carousel-control-next-icon"></span>
        </button>

    </div>

</div>

</form>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>






SELECT 
    M.ID AS ModuleId,
    M.ModuleName,
    A.Attachments AS ModuleImage,
    Q.Id AS QuestionId,
    Q.QuestionType,
    Q.Question,
    Q.Option1,
    Q.Option2,
    Q.Option3,
    Q.Option4,
    Q.Ans,
    Q.QuestionImage,
    Q.SeqNo
FROM App_Module_Master M
LEFT JOIN App_Module_Attachments A 
    ON A.ModuleID = M.ID AND A.SeqNo = 1 AND A.IsActive = 1
LEFT JOIN App_QuestionMaster Q 
    ON Q.ModuleID = M.ID AND Q.IsActive = 1
WHERE M.IsActive = 1
ORDER BY M.SerialNo, Q.SeqNo;

<div id="quizCarousel" class="carousel slide" data-bs-interval="false">
    <div class="carousel-inner">

        <asp:Repeater ID="rptSlides" runat="server">
            <ItemTemplate>

                <div class='carousel-item <%# Container.ItemIndex == 0 ? "active" : "" %>'>

                    <!-- MODULE SLIDE -->
                    <asp:Panel runat="server" Visible='<%# Eval("SlideType").ToString() == "MODULE" %>'>
                        <div class="card text-center p-4 shadow">
                            <img src='<%# ResolveUrl("~/Upload/" + Eval("ModuleImage")) %>'
                                 class="img-fluid mb-3"
                                 style="height:260px; object-fit:cover;" />

                            <h2><%# Eval("ModuleName") %></h2>
                            <p class="text-muted">
                                <%# Eval("QuestionCount") %> Questions
                            </p>
                        </div>
                    </asp:Panel>

                    <!-- QUESTION SLIDE -->
                    <asp:Panel runat="server" Visible='<%# Eval("SlideType").ToString() == "QUESTION" %>'>
                        <div class="card p-4 shadow">

                            <h5 class="text-muted mb-2">
                                <%# Eval("ModuleName") %>
                            </h5>

                            <h4><%# Eval("Question") %></h4>

                            <!-- IMAGE -->
                            <asp:Image runat="server"
                                Visible='<%# Eval("QuestionImage") != DBNull.Value %>'
                                ImageUrl='<%# ResolveUrl("~/Upload/" + Eval("QuestionImage")) %>'
                                CssClass="img-fluid my-3" />

                            <!-- OBJECTIVE -->
                            <asp:RadioButtonList runat="server"
                                Visible='<%# Eval("QuestionType").ToString() == "Objective" %>'>
                                <asp:ListItem Text='<%# Eval("Option1") %>' Value="1" />
                                <asp:ListItem Text='<%# Eval("Option2") %>' Value="2" />
                                <asp:ListItem Text='<%# Eval("Option3") %>' Value="3" />
                                <asp:ListItem Text='<%# Eval("Option4") %>' Value="4" />
                            </asp:RadioButtonList>

                            <!-- SUBJECTIVE -->
                            <asp:TextBox runat="server"
                                Visible='<%# Eval("QuestionType").ToString() == "Subjective" %>'
                                CssClass="form-control mt-3"
                                TextMode="MultiLine" Rows="3" />

                        </div>
                    </asp:Panel>

                </div>

            </ItemTemplate>
        </asp:Repeater>

    </div>

    <!-- Controls -->
    <button class="carousel-control-prev" type="button"
            data-bs-target="#quizCarousel" data-bs-slide="prev"></button>

    <button class="carousel-control-next" type="button"
            data-bs-target="#quizCarousel" data-bs-slide="next"></button>
</div>

protected void Page_Load(object sender, EventArgs e)
{
    if (!IsPostBack)
        LoadSlides();
}

void LoadSlides()
{
    DataTable slides = new DataTable();
    slides.Columns.Add("SlideType");
    slides.Columns.Add("ModuleId");
    slides.Columns.Add("ModuleName");
    slides.Columns.Add("ModuleImage");
    slides.Columns.Add("QuestionCount");
    slides.Columns.Add("Question");
    slides.Columns.Add("QuestionType");
    slides.Columns.Add("Option1");
    slides.Columns.Add("Option2");
    slides.Columns.Add("Option3");
    slides.Columns.Add("Option4");
    slides.Columns.Add("QuestionImage");

    string cs = ConfigurationManager.ConnectionStrings["ApplicationServices"].ConnectionString;

    using (SqlConnection con = new SqlConnection(cs))
    using (SqlCommand cmd = new SqlCommand(SQL_QUERY_HERE, con))
    {
        con.Open();
        using (SqlDataReader dr = cmd.ExecuteReader())
        {
            string lastModule = "";

            while (dr.Read())
            {
                string moduleId = dr["ModuleId"].ToString();

                if (lastModule != moduleId)
                {
                    // MODULE SLIDE
                    DataRow m = slides.NewRow();
                    m["SlideType"] = "MODULE";
                    m["ModuleId"] = moduleId;
                    m["ModuleName"] = dr["ModuleName"];
                    m["ModuleImage"] = dr["ModuleImage"] ?? "";
                    m["QuestionCount"] = GetQuestionCount(moduleId);
                    slides.Rows.Add(m);

                    lastModule = moduleId;
                }

                if (dr["QuestionId"] != DBNull.Value)
                {
                    DataRow q = slides.NewRow();
                    q["SlideType"] = "QUESTION";
                    q["ModuleId"] = moduleId;
                    q["ModuleName"] = dr["ModuleName"];
                    q["Question"] = dr["Question"];
                    q["QuestionType"] = dr["QuestionType"];
                    q["Option1"] = dr["Option1"];
                    q["Option2"] = dr["Option2"];
                    q["Option3"] = dr["Option3"];
                    q["Option4"] = dr["Option4"];
                    q["QuestionImage"] = dr["QuestionImage"];
                    slides.Rows.Add(q);
                }
            }
        }
    }

    rptSlides.DataSource = slides;
    rptSlides.DataBind();
}





<asp:Image runat="server"
    ImageUrl='<%# string.IsNullOrEmpty(Eval("CoverImage").ToString()) 
        ? ResolveUrl("~/Images/no-image.png") 
        : ResolveUrl("~/Upload/" + Eval("CoverImage")) %>'
    CssClass="img-fluid rounded mb-3"
    Style="height:250px; object-fit:cover;" />


<asp:Repeater ID="rptModules" runat="server"
              OnItemCommand="rptModules_ItemCommand">

protected void rptModules_ItemCommand(object source, RepeaterCommandEventArgs e)
{
    if (e.CommandName == "StartQuiz")
    {
        string moduleId = e.CommandArgument.ToString();
        Response.Redirect("Quiz.aspx?ModuleId=" + moduleId);
    }
}



error : 

QuestionResult.aspx:233  GET http://localhost:6492/Upload/ 403 (Forbidden)
QuestionResult.aspx:318  GET http://localhost:6492/Upload/ 403 (Forbidden)


<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">

    <div id="quizCarousel" class="carousel slide" data-bs-ride="carousel">
    <div class="carousel-inner">

        <asp:Repeater ID="rptModules" runat="server">
            <ItemTemplate>
                <div class='carousel-item <%# Container.ItemIndex == 0 ? "active" : "" %>'>
                    <div class="card text-center shadow p-4">

                        <img src='<%# ResolveUrl("~/Upload/" + Eval("CoverImage")) %>' 
                             class="img-fluid rounded mb-3"
                             style="height:250px; object-fit:cover;" />

                        <h3><%# Eval("ModuleName") %></h3>

                        <p class="text-muted">
                            <%# Eval("QuestionCount") %> Questions
                        </p>

                        <asp:Button runat="server"
                            Text="Start Quiz"
                            CssClass="btn btn-primary"
                            CommandName="StartQuiz"
                            CommandArgument='<%# Eval("ID") %>' />
                    </div>
                </div>
            </ItemTemplate>
        </asp:Repeater>

    </div>

    <!-- Controls -->
    <button class="carousel-control-prev" type="button" data-bs-target="#quizCarousel" data-bs-slide="prev">
        <span class="carousel-control-prev-icon"></span>
    </button>

    <button class="carousel-control-next" type="button" data-bs-target="#quizCarousel" data-bs-slide="next">
        <span class="carousel-control-next-icon"></span>
    </button>
</div>

</asp:Content>


        protected void Page_Load(object sender, EventArgs e)
        {
            if (!IsPostBack)
            {
                LoadModules();
            }
        }

        private void LoadModules()
        {

            string cs = ConfigurationManager.ConnectionStrings["ApplicationServices"].ConnectionString;



            string sql = @"SELECT 
    M.ID,
    M.ModuleName,
    M.SerialNo,
    ISNULL(A.Attachments, '') AS CoverImage,
    COUNT(Q.Id) AS QuestionCount
FROM App_Module_Master M
LEFT JOIN App_Module_Attachments A 
    ON A.ModuleID = M.ID 
    AND A.IsActive = 1 
    AND A.SeqNo = 1
LEFT JOIN App_QuestionMaster Q 
    ON Q.ModuleID = M.ID 
    AND Q.IsActive = 1
WHERE M.IsActive = 1
GROUP BY M.ID, M.ModuleName, M.SerialNo, A.Attachments
ORDER BY M.SerialNo;";

            using (SqlConnection con = new SqlConnection(cs))
            using (SqlCommand cmd = new SqlCommand(sql, con))
            {
                con.Open();
                rptModules.DataSource = cmd.ExecuteReader();
                rptModules.DataBind();
            }
        }

        protected void rptModules_ItemCommand(object source, RepeaterCommandEventArgs e)
        {
            if (e.CommandName == "StartQuiz")
            {
                string moduleId = e.CommandArgument.ToString();
                Response.Redirect("QuizResult.aspx?ModuleId=" + moduleId);
            }
        }
