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


<div class="mb-3">
    <div class="d-flex justify-content-between align-items-center mb-1">
        <small class="text-muted" id="progressText">0%</small>
        <small class="text-muted" id="slideCounter">0 / 0</small>
    </div>

    <div class="progress" style="height: 10px; border-radius: 20px;">
        <div id="quizProgressBar"
             class="progress-bar progress-bar-striped progress-bar-animated bg-warning"
             role="progressbar"
             style="width: 0%; border-radius: 20px;">
        </div>
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


css

    <style type="text/css">
        a {
    color:#fff;
    text-decoration: none;
}

#quizCarousel {
    max-width: 900px;
    margin: auto;
}

.carousel-item {
    min-height: 520px;
}


.carousel-inner {
    padding-left: 70px;
    padding-right: 70px;
}

.quiz-card {
    min-height: 520px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    padding: 30px;
    position: relative;
    z-index: 5;
}

.quiz-image {
    max-height: 460px;
    object-fit: contain;
}


.carousel-control-prev,
.carousel-control-next {
    width: 50px;
    z-index: 2;
}

.carousel-control-prev {
    left: 10px;
}

.carousel-control-next {
    right: 10px;
}

.carousel-control-prev-icon,
.carousel-control-next-icon {
    background-color: rgba(0,0,0,0.6);
    border-radius: 50%;
    padding: 20px;
}


@media (max-width: 768px) {
    .carousel-inner {
        padding-left: 50px;
        padding-right: 50px;
    }

    .quiz-card {
        min-height: 420px;
    }
}

.quiz-options {
    margin-top: 20px;
}


.quiz-options input[type="radio"] {
    display: none;
}


.quiz-options label {
    display: block;
    padding: 16px 18px;
    margin-bottom: 14px;
    border: 1.5px solid #dee2e6;
    border-radius: 12px;
    cursor: pointer;
    transition: all 0.25s ease;
    background-color: #fff;
    font-size: 16px;
}


.quiz-options label:hover {
    border-color: #0d6efd;
    background-color: #f8f9fa;
}


.quiz-options input[type="radio"]:checked + label {
    border-color: #0d6efd;
    background-color: #e7f1ff;
    box-shadow: 0 0 0 2px rgba(13,110,253,0.15);
    font-weight: 600;
}

.quiz-options label {
    animation: fadeIn 0.2s ease;
}

@keyframes fadeIn {
    from { opacity: 0; transform: translateY(3px); }
    to { opacity: 1; transform: translateY(0); }
}


@media (max-width: 768px) {
    .quiz-options label {
        font-size: 15px;
        padding: 14px;
    }
}


.quiz-options input[type="radio"].correct + label {
    border-color: #198754 !important;
    background-color: #e9f7ef !important;
    color: #198754;
}


.quiz-options input[type="radio"].wrong + label {
    border-color: #dc3545 !important;
    background-color: #fdecea !important;
    color: #dc3545;
}
.locked {
    pointer-events: none;
    opacity: 0.8;
}

 .progress {
        background: #e9ecef;
        overflow: hidden;
    }

    #quizProgressBar {
        transition: width 0.4s ease-in-out;
    }


    </style>
