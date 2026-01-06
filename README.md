        void LoadSlides()
        {
            DataTable dt = new DataTable();

            dt.Columns.Add("SlideType"); // ATTACHMENT / QUESTION
            dt.Columns.Add("ModuleId");
            dt.Columns.Add("ModuleName");

            // Attachment
            dt.Columns.Add("Attachment");

            // Question
            dt.Columns.Add("QuestionType");
            dt.Columns.Add("Question");
            dt.Columns.Add("Option1");
            dt.Columns.Add("Option2");
            dt.Columns.Add("Option3");
            dt.Columns.Add("Option4");
            dt.Columns.Add("QuestionImage");

            string cs = ConfigurationManager.ConnectionStrings["ApplicationServices"].ConnectionString;

            string sql = @"SELECT 
    M.ID AS ModuleId,
    M.ModuleName,

    A.Attachments,
    A.SeqNo AS AttachmentSeq,

    Q.Id AS QuestionId,
    Q.QuestionType,
    Q.Question,
    Q.Option1,
    Q.Option2,
    Q.Option3,
    Q.Option4,
    Q.QuestionImage,
    Q.SeqNo AS QuestionSeq

FROM App_Module_Master M

LEFT JOIN App_Module_Attachments A
    ON A.ModuleID = M.ID AND A.IsActive = 1

LEFT JOIN App_QuestionMaster Q
    ON Q.ModuleID = M.ID AND Q.IsActive = 1

WHERE M.IsActive = 1
ORDER BY 
    M.SerialNo,
    A.SeqNo,
    Q.SeqNo;";

            using (SqlConnection con = new SqlConnection(cs))
            using (SqlCommand cmd = new SqlCommand(sql, con))
            {
                con.Open();
                SqlDataReader dr = cmd.ExecuteReader();

                string currentModule = "";
                HashSet<string> addedAttachments = new HashSet<string>();
                HashSet<string> addedQuestions = new HashSet<string>();

                while (dr.Read())
                {
                    string moduleId = dr["ModuleId"].ToString();

                    // reset per module
                    if (currentModule != moduleId)
                    {
                        addedAttachments.Clear();
                        addedQuestions.Clear();
                        currentModule = moduleId;
                    }

                    // ---------------- ATTACHMENT SLIDES ----------------
                    if (dr["Attachments"] != DBNull.Value)
                    {
                        string attachment = dr["Attachments"].ToString();

                        if (!addedAttachments.Contains(attachment))
                        {
                            DataRow a = dt.NewRow();
                            a["SlideType"] = "ATTACHMENT";
                            a["ModuleId"] = moduleId;
                            a["ModuleName"] = dr["ModuleName"];
                            a["Attachment"] = attachment;

                            dt.Rows.Add(a);
                            addedAttachments.Add(attachment);
                        }
                    }

                    // ---------------- QUESTION SLIDES ----------------
                    if (dr["QuestionId"] != DBNull.Value)
                    {
                        string qId = dr["QuestionId"].ToString();

                        if (!addedQuestions.Contains(qId))
                        {
                            DataRow q = dt.NewRow();
                            q["SlideType"] = "QUESTION";
                            q["ModuleId"] = moduleId;
                            q["ModuleName"] = dr["ModuleName"];
                            q["QuestionType"] = dr["QuestionType"];
                            q["Question"] = dr["Question"];
                            q["Option1"] = dr["Option1"];
                            q["Option2"] = dr["Option2"];
                            q["Option3"] = dr["Option3"];
                            q["Option4"] = dr["Option4"];
                            q["QuestionImage"] = dr["QuestionImage"];

                            dt.Rows.Add(q);
                            addedQuestions.Add(qId);
                        }
                    }
                }
            }

            rptSlides.DataSource = dt;
            rptSlides.DataBind();
        }


        protected void rptSlides_ItemDataBound(object sender, RepeaterItemEventArgs e)
        {
            if (e.Item.ItemType != ListItemType.Item &&
                e.Item.ItemType != ListItemType.AlternatingItem)
                return;

            DataRowView row = (DataRowView)e.Item.DataItem;

            if (row["SlideType"].ToString() != "QUESTION")
                return;

  
            Image img = (Image)e.Item.FindControl("imgQuestion");
            if (row["QuestionImage"] != DBNull.Value)
            {
                img.ImageUrl = "~/Upload/" + row["QuestionImage"];
                img.Visible = true;
            }

            string qType = row["QuestionType"].ToString();


            if (qType == "Objective")
            {
                RadioButtonList rbl = (RadioButtonList)e.Item.FindControl("rblOptions");
                rbl.Visible = true;
                rbl.Items.Clear();

                rbl.Items.Add(new ListItem(row["Option1"].ToString(), "1"));
                rbl.Items.Add(new ListItem(row["Option2"].ToString(), "2"));
                rbl.Items.Add(new ListItem(row["Option3"].ToString(), "3"));
                rbl.Items.Add(new ListItem(row["Option4"].ToString(), "4"));
            }

            if (qType == "Subjective")
            {
                TextBox txt = (TextBox)e.Item.FindControl("txtAnswer");
                txt.Visible = true;
            }
        }

        int GetQuestionCount(string moduleId)
        {
            string cs = ConfigurationManager.ConnectionStrings["ApplicationServices"].ConnectionString;

            using (SqlConnection con = new SqlConnection(cs))
            using (SqlCommand cmd = new SqlCommand(
                "SELECT COUNT(*) FROM App_QuestionMaster WHERE ModuleID = @ModuleId AND IsActive = 1", con))
            {
                cmd.Parameters.AddWithValue("@ModuleId", moduleId);
                con.Open();
                return Convert.ToInt32(cmd.ExecuteScalar());
            }
        }


<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">

<div class="container mt-4">

    <div id="quizCarousel" class="carousel slide" data-bs-interval="false">
        <div class="carousel-inner">

            <asp:Repeater ID="rptSlides"
                runat="server"
                OnItemDataBound="rptSlides_ItemDataBound">

                <ItemTemplate>

                    <div class='carousel-item <%# Container.ItemIndex == 0 ? "active" : "" %>'>

                        <!-- ================= MODULE SLIDE ================= -->
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

                        <!-- ================= QUESTION SLIDE ================= -->
                        <asp:Panel runat="server"
                            Visible='<%# Eval("SlideType").ToString() == "QUESTION" %>'>

                            <div class="card shadow p-4">

                                <h6 class="text-muted">
                                    <%# Eval("ModuleName") %>
                                </h6>

                                <h5 class="mb-3">
                                    <%# Eval("Question") %>
                                </h5>

                                <!-- Question Image -->
                                <asp:Image runat="server"
                                    ID="imgQuestion"
                                    CssClass="img-fluid mb-3"
                                    Visible="false" />

                                <!-- Objective -->
                                <asp:RadioButtonList
                                    ID="rblOptions"
                                    runat="server"
                                    CssClass="list-group"
                                    Visible="false">
                                </asp:RadioButtonList>

                                <!-- Subjective -->
                                <asp:TextBox
                                    ID="txtAnswer"
                                    runat="server"
                                    CssClass="form-control"
                                    TextMode="MultiLine"
                                    Rows="3"
                                    Visible="false" />

                            </div>

                        </asp:Panel>

                    </div>

                </ItemTemplate>
            </asp:Repeater>

        </div>

        <asp:Panel runat="server"
    Visible='<%# Eval("SlideType").ToString() == "ATTACHMENT" %>'>

    <div class="card shadow text-center p-4">

        <asp:Image runat="server"
            ImageUrl='<%# ResolveUrl("~/Upload/" + Eval("Attachment")) %>'
            CssClass="img-fluid"
            Style="max-height:400px; object-fit:contain;" />

        <h5 class="mt-3 text-muted">
            <%# Eval("ModuleName") %>
        </h5>

    </div>
</asp:Panel>

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


    DataBinding: 'System.Data.DataRowView' does not contain a property with the name 'ModuleImage'.

</div>


    <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>

</asp:Content>
