<div class="container mt-4">


    <div id="quizCompleted" class="text-center mt-5 d-none">
        <div class="card shadow p-5">
            <h3 class="text-success mb-3">🎉 Quiz Completed!</h3>
            <p class="text-muted">Thank you for completing the quiz.</p>
        </div>
    </div>


    <div id="quizAlreadyCompleted" class="text-center mt-5 d-none">
        <div class="card shadow p-5">
            <h3 class="text-info mb-3">✅ Quiz Already Completed</h3>
            <p class="text-muted">You have already completed this quiz.</p>
        </div>
    </div>

    <!-- Carousel -->
    <div id="quizCarousel" class="carousel slide" data-bs-interval="false">
        <div class="carousel-inner">

            <asp:Repeater ID="rptSlides" runat="server" OnItemDataBound="rptSlides_ItemDataBound">
                <ItemTemplate>

                    <div class='carousel-item 
                        <%# Container.ItemIndex == 0 ? "active" : "" %>
                        <%# Eval("SlideType").ToString() == "QUESTION" ? "quiz-slide" : "attachment-slide" %>'
                        data-moduleid='<%# Eval("ModuleId") %>'
                        data-questionid='<%# Eval("QuestionId") %>'>

                        <!-- Attachment Slide -->
                        <asp:Panel runat="server" Visible='<%# Eval("SlideType").ToString() == "ATTACHMENT" %>'>
                            <div class="card shadow quiz-card text-center">
                                <asp:Image runat="server"
                                    ImageUrl='<%# ResolveUrl("~/Upload/" + Eval("Attachment")) %>'
                                    CssClass="img-fluid quiz-image mx-auto" />
                                <h5 class="mt-4 text-muted">
                                    <%# Eval("ModuleName") %>
                                </h5>
                            </div>
                        </asp:Panel>

                        <!-- Question Slide -->
                        <asp:Panel runat="server" Visible='<%# Eval("SlideType").ToString() == "QUESTION" %>'>
                            <div class="card shadow quiz-card">

                                <div class="text-center mb-3">
                                    <span class="badge bg-dark">
                                        <%# Eval("ModuleName") %>
                                    </span>
                                </div>

                                <h5 class="quiz-question text-center mb-4">
                                    <%# Eval("Question") %>
                                </h5>

                                <asp:Image runat="server"
                                    ID="imgQuestion"
                                    CssClass="img-fluid quiz-image mx-auto mb-3"
                                    Visible="false" />

                                <asp:RadioButtonList
                                    ID="rblOptions"
                                    runat="server"
                                    CssClass="quiz-options"
                                    RepeatDirection="Vertical"
                                    Visible="false">
                                </asp:RadioButtonList>

                                <asp:TextBox
                                    ID="txtAnswer"
                                    runat="server"
                                    CssClass="form-control mt-3"
                                    TextMode="MultiLine"
                                    Rows="2"
                                    Visible="false" />
                            </div>

                            <asp:HiddenField ID="hfModuleId" runat="server" />
                            <asp:HiddenField ID="hfQuestionId" runat="server" />
                        </asp:Panel>

                    </div>

                </ItemTemplate>
            </asp:Repeater>

        </div>

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

<asp:HiddenField ID="hfResumeQuestionId" runat="server" ClientIDMode="Static" />
<asp:HiddenField ID="hfResumeModuleId" runat="server" ClientIDMode="Static" />
<asp:HiddenField ID="hfGoToAttachment" runat="server" ClientIDMode="Static" />
<asp:HiddenField ID="hfQuizCompleted" runat="server" ClientIDMode="Static" />

