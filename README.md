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
