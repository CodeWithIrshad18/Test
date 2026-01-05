SELECT 
    m.ID AS ModuleId,
    m.ModuleName,
    ISNULL(a.Attachments, '') AS ModuleImage,
    COUNT(q.Id) AS TotalQuestions
FROM App_Module_Master m
LEFT JOIN App_Module_Attachments a 
    ON a.ModuleID = m.ID AND a.SeqNo = 1 AND a.IsActive = 1
LEFT JOIN App_QuestionMaster q 
    ON q.ModuleID = m.ID AND q.IsActive = 1
WHERE m.IsActive = 1
GROUP BY m.ID, m.ModuleName, a.Attachments
ORDER BY m.SerialNo;

<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet" />

<div id="moduleCarousel" class="carousel slide" data-bs-ride="carousel">
    <div class="carousel-inner">

        <asp:Repeater ID="rptModules" runat="server">
            <ItemTemplate>
                <div class="carousel-item <%# Container.ItemIndex == 0 ? "active" : "" %>">

                    <img src='<%# "/Uploads/" + Eval("ModuleImage") %>' 
                         class="d-block w-100" style="height:300px;" />

                    <div class="carousel-caption bg-dark bg-opacity-75">
                        <h5><%# Eval("ModuleName") %></h5>
                        <p><%# Eval("TotalQuestions") %> Questions</p>

                        <asp:Button 
                            runat="server"
                            Text="Start Quiz"
                            CssClass="btn btn-primary"
                            PostBackUrl='<%# "Quiz.aspx?mid=" + Eval("ModuleId") %>' />
                    </div>

                </div>
            </ItemTemplate>
        </asp:Repeater>

    </div>
</div>

protected void Page_Load(object sender, EventArgs e)
{
    if (!IsPostBack)
    {
        LoadModules();
    }
}

private void LoadModules()
{
    using (SqlConnection con = new SqlConnection(ConfigurationManager.ConnectionStrings["DefaultConnection"].ConnectionString))
    {
        SqlCommand cmd = new SqlCommand("YOUR_QUERY_HERE", con);
        SqlDataAdapter da = new SqlDataAdapter(cmd);
        DataTable dt = new DataTable();
        da.Fill(dt);

        rptModules.DataSource = dt;
        rptModules.DataBind();
    }
}

<asp:HiddenField ID="hfIndex" runat="server" Value="0" />

<asp:Label ID="lblQuestion" runat="server" CssClass="h5" />

<asp:RadioButtonList ID="rblOptions" runat="server" />

<asp:TextBox ID="txtAnswer" runat="server" TextMode="MultiLine" Visible="false" />

<br />

<asp:Button ID="btnNext" runat="server" Text="Next" OnClick="btnNext_Click" />

DataTable Questions
{
    get { return (DataTable)Session["Questions"]; }
    set { Session["Questions"] = value; }
}

protected void Page_Load(object sender, EventArgs e)
{
    if (!IsPostBack)
    {
        LoadQuestions();
        BindQuestion(0);
    }
}

private void LoadQuestions()
{
    Guid moduleId = Guid.Parse(Request.QueryString["mid"]);

    using (SqlConnection con = new SqlConnection(cs))
    {
        SqlCommand cmd = new SqlCommand(
            "SELECT * FROM App_QuestionMaster WHERE ModuleID=@mid AND IsActive=1 ORDER BY SeqNo", con);

        cmd.Parameters.AddWithValue("@mid", moduleId);

        SqlDataAdapter da = new SqlDataAdapter(cmd);
        DataTable dt = new DataTable();
        da.Fill(dt);

        Questions = dt;
    }
}

private void BindQuestion(int index)
{
    DataRow row = Questions.Rows[index];

    lblQuestion.Text = row["Question"].ToString();

    if (row["QuestionType"].ToString() == "Objective")
    {
        rblOptions.Visible = true;
        txtAnswer.Visible = false;

        rblOptions.Items.Clear();
        rblOptions.Items.Add(row["Option1"].ToString());
        rblOptions.Items.Add(row["Option2"].ToString());
        rblOptions.Items.Add(row["Option3"].ToString());
        rblOptions.Items.Add(row["Option4"].ToString());
    }
    else
    {
        rblOptions.Visible = false;
        txtAnswer.Visible = true;
    }

    hfIndex.Value = index.ToString();
}

protected void btnNext_Click(object sender, EventArgs e)
{
    int index = int.Parse(hfIndex.Value) + 1;

    if (index < Questions.Rows.Count)
        BindQuestion(index);
    else
        Response.Redirect("Result.aspx");
}





SELECT 
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
ORDER BY M.SerialNo;

<div id="quizCarousel" class="carousel slide" data-bs-ride="carousel">
    <div class="carousel-inner">

        <asp:Repeater ID="rptModules" runat="server">
            <ItemTemplate>
                <div class='carousel-item <%# Container.ItemIndex == 0 ? "active" : "" %>'>
                    <div class="card text-center shadow p-4">

                        <img src='<%# ResolveUrl("~/Uploads/" + Eval("CoverImage")) %>' 
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


protected void Page_Load(object sender, EventArgs e)
{
    if (!IsPostBack)
    {
        LoadModules();
    }
}

private void LoadModules()
{
    string sql = @" -- use query above ";

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
        Response.Redirect("Quiz.aspx?ModuleId=" + moduleId);
    }
}




select * from App_Module_Master

ID	ModuleName	CreatedOn	CreatedBy	SerialNo	IsActive
8974E29B-269D-4707-BB44-39CA88285D37	Instructions	2025-09-26 11:09:29.000	159445	3	1
7B5A68D0-A5E7-48A6-996D-409C15DC39DE	Milestone3	2025-09-26 11:09:29.000	159445	4	0
12ED0FE6-D3D3-4A13-BCCB-6E7272FDADF7	Home Page	2025-09-26 11:09:29.000	159445	1	1
45F01FDE-0D73-46E1-8D34-9976B3C4E1DE	Milestone4	2025-09-26 11:09:29.000	159445	5	1
041AC05D-822D-4597-9F89-C0389A56AD13	CHRO Message	2025-09-26 11:09:29.000	159445	2	1
FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Milestone1	2025-09-26 11:09:29.000	159445	6	1


select * from App_Module_Attachments

ID	SeqNo	ModuleID	CreatedOn	CreatedBy	Attachments	IsActive
F4C38618-403B-4AC3-A152-0F63582E8AB4	1	12ED0FE6-D3D3-4A13-BCCB-6E7272FDADF7	2025-12-17 18:24:38.000	159445	f4c38618-403b-4ac3-a152-0f63582e8ab4Screenshot 2025-12-17 170622.png	1
87959E18-BA98-4BED-A86D-10F22E39D4F8	1	060AAC72-6129-4528-866A-0BD262160D4D	2025-11-18 16:05:40.000	159445	87959e18-ba98-4bed-a86d-10f22e39d4f81fb228ea-47b9-48da-a9b0-2fd93d886c5cSlide19.PNG	1
9196DC1C-DCF5-4E37-A8A1-1EF341AE696E	3	8974E29B-269D-4707-BB44-39CA88285D37	2025-12-17 18:30:50.000	159445	9196dc1c-dcf5-4e37-a8a1-1ef341ae696eScreenshot 2025-12-17 182925.png	1
43149B4F-61D7-440B-B87C-3DC13BBEAFFB	4	FCB7ADB8-E802-4957-9410-C3B4B354CDDA	2025-10-31 17:47:46.000	159445	cf260542-6b9f-42c8-a0be-b2c2865d5aa2Slide14.PNG	1
F758944D-07D2-4E4A-81D9-44991A070C30	2	041AC05D-822D-4597-9F89-C0389A56AD13	2025-10-31 17:47:06.000	159445	f758944d-07d2-4e4a-81d9-44991a070c30Screenshot 2025-12-17 181535.png	1
6A0FA8C9-DB39-42B9-85AF-A10F04D77166	5	FCB7ADB8-E802-4957-9410-C3B4B354CDDA	2025-10-31 17:47:46.000	159445	43149b4f-61d7-440b-b87c-3dc13bbeaffbScreenshot 2025-12-18 095343.png	1
CF260542-6B9F-42C8-A0BE-B2C2865D5AA2	6	FCB7ADB8-E802-4957-9410-C3B4B354CDDA	2025-10-31 17:47:45.000	159445	43149b4f-61d7-440b-b87c-3dc13bbeaffbScreenshot 2025-12-18 095419.png	1
8FC15F8A-F217-4460-A9A5-CDE55590A72B	7	FCB7ADB8-E802-4957-9410-C3B4B354CDDA	2025-11-14 12:25:09.000	159445	43149b4f-61d7-440b-b87c-3dc13bbeaffbScreenshot 2025-12-18 095646.png	1


select * from App_QuestionMaster

Id	ModuleID	QuestionType	Question	Option1	Option2	Option3	Option4	Ans	QuestionImage	IsActive	CreatedOn	UpdatedOn	CreatedBy	UpdatedBy	SeqNo
933B1D16-A4A6-45FF-B334-1253F335E1BA	45F01FDE-0D73-46E1-8D34-9976B3C4E1DE	Subjective	Waterfall to visit in Sukhinda					1	NULL	1	2025-11-11 16:11:50.053	NULL	159445	NULL	1
74E97AF3-4555-46CA-8158-1BD2BBA617F2	FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Objective	In which year did Tata Steel UISL complete 20 years of quality service	2022	2023	2024	2021	2	74e97af3-4555-46ca-8158-1bd2bba617f2Slide15.PNG	1	2025-11-06 12:42:16.500	NULL	159445	NULL	9
1FB228EA-47B9-48DA-A9B0-2FD93D886C5C	FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Objective	Which is our correct Vision statement 	We shall be a provider of best value infrastructure & utility services	We shall be the provider to best value infrastructure and quality service	We shall be the provider of quality services for life ?	We shall the be the provider of best infrastructure and utility services y	1	1fb228ea-47b9-48da-a9b0-2fd93d886c5cScreenshot 2025-10-28 105943.png	1	2025-10-29 18:04:00.267	NULL	159445	NULL	8
72C9AFC8-8DFE-4DCC-8506-4F0DCC0D6BC5	8974E29B-269D-4707-BB44-39CA88285D37	Subjective	Name our HOD for Town (O&M)?					1	72c9afc8-8dfe-4dcc-8506-4f0dcc0d6bc5Slide19.PNG	1	2025-11-06 12:51:18.170	NULL	159445	NULL	3
4C963F40-E1C6-4577-AE8A-64062A067E26	8974E29B-269D-4707-BB44-39CA88285D37	Subjective	Mention the list of Business Units ?					1	4c963f40-e1c6-4577-ae8a-64062a067e26Slide18.PNG	1	2025-11-06 12:50:26.823	NULL	159445	NULL	2
326FF8EF-5418-4033-8541-7604CD2100B9	8974E29B-269D-4707-BB44-39CA88285D37	Subjective	Name two states where we have our existence					1	326ff8ef-5418-4033-8541-7604cd2100b9Slide17.PNG	1	2025-11-06 12:49:32.020	NULL	159445	NULL	1
C2BB258D-3FE3-4195-91DE-82370D99CC03	12ED0FE6-D3D3-4A13-BCCB-6E7272FDADF7	Subjective	Name the wildlife sanctuary in Jamshedpur ?					1	NULL	1	2025-11-11 16:42:53.007	NULL	159445	NULL	1
E95ABAD3-23E7-416C-AF53-86656A5C9D57	FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Objective	In which year did Tata Steel UISL stand for	Tata Steel Utilities and Infrastructure Services Limited	Tata Steel Utilities and Infra Service Limited	Tata Steel Utility and Infra Structure Limited	Tata Steel Utilities Infrastructure Services Limited	1	e95abad3-23e7-416c-af53-86656a5c9d57Slide13.PNG	1	2025-11-06 12:47:25.730	NULL	159445	NULL	11
6B96E17F-5A07-4E07-9CB5-B0B9164CF36C	7B5A68D0-A5E7-48A6-996D-409C15DC39DE	Subjective	When do we celebrate our Founder’s Day?					1	NULL	1	2025-11-11 16:10:58.440	NULL	159445	NULL	1
B30D49F4-E11B-4579-ABB5-B6A4B5AA281E	FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Objective	In which year was Tata Steel UISL renamed	2019	2020	2021	2022	1	b30d49f4-e11b-4579-abb5-b6a4b5aa281eSlide16.PNG	1	2025-11-06 12:43:37.033	NULL	159445	NULL	10
7B2C7662-C89D-4148-8615-BEE356B3491D	FCB7ADB8-E802-4957-9410-C3B4B354CDDA	Objective	Which set of values correctly represents the company’s core principles?	Integrity, Unity, Pioneering	Responsibility, Agility, Innovation	Integrity, Agility, Collaboration	Unity, Responsibility, Sustainability	1	NULL	1	2025-12-18 10:47:50.000	NULL	159445	NULL	12
8A988661-D2D7-45D2-BB00-C300AEB9DB1B	8974E29B-269D-4707-BB44-39CA88285D37	Subjective	Name the CFO of our Organization?					1	8a988661-d2d7-45d2-bb00-c300aeb9db1bSlide20.PNG	1	2025-11-06 12:51:52.717	NULL	159445	NULL	4
F151F9B1-F27C-4777-8F79-DC88AB662185	8974E29B-269D-4707-BB44-39CA88285D37	Objective	Name the state where we are present ?	Uttarakhand?	Madhya Pradesh?	Odisha	Uttar Pradesh	1	NULL	1	2025-11-06 12:57:08.190	NULL	159445	NULL	5




<%@ Page Title="" Language="C#" MasterPageFile="~/Site.Master" AutoEventWireup="true" CodeBehind="QuestionResult.aspx.cs" Inherits="POS.App.Input.QuestionResult" %>
<asp:Content ID="Content1" ContentPlaceHolderID="HeadContent" runat="server">
</asp:Content>
<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">
</asp:Content>
