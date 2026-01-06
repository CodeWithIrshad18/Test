<style>
/* Carousel container */
#quizCarousel {
    max-width: 900px;
    margin: auto;
}

/* All carousel items same height */
.carousel-item {
    min-height: 520px;
}

/* Card layout */
.quiz-card {
    min-height: 520px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    padding: 30px;
}

/* Attachment image */
.quiz-image {
    max-height: 360px;
    object-fit: contain;
}

/* Question alignment */
.quiz-question {
    margin-top: 10px;
}

/* Better carousel controls */
.carousel-control-prev,
.carousel-control-next {
    width: 8%;
}

.carousel-control-prev-icon,
.carousel-control-next-icon {
    background-color: rgba(0,0,0,0.6);
    border-radius: 50%;
    padding: 20px;
}

/* Radio buttons spacing */
.list-group .form-check {
    margin-bottom: 10px;
}

/* Mobile responsiveness */
@media (max-width: 768px) {
    .carousel-item,
    .quiz-card {
        min-height: 420px;
    }
}
</style>


<asp:Repeater ID="rptSlides"
    runat="server"
    OnItemDataBound="rptSlides_ItemDataBound">

    <ItemTemplate>

        <div class='carousel-item <%# Container.ItemIndex == 0 ? "active" : "" %>'>

            <!-- ========== ATTACHMENT SLIDE ========== -->
            <asp:Panel runat="server"
                Visible='<%# Eval("SlideType").ToString() == "ATTACHMENT" %>'>

                <div class="card shadow quiz-card text-center">

                    <asp:Image runat="server"
                        ImageUrl='<%# ResolveUrl("~/Upload/" + Eval("Attachment")) %>'
                        CssClass="img-fluid quiz-image mx-auto" />

                    <h5 class="mt-4 text-muted">
                        <%# Eval("ModuleName") %>
                    </h5>

                </div>
            </asp:Panel>

            <!-- ========== QUESTION SLIDE ========== -->
            <asp:Panel runat="server"
                Visible='<%# Eval("SlideType").ToString() == "QUESTION" %>'>

                <div class="card shadow quiz-card">

                    <div class="text-center mb-3">
                        <span class="badge bg-secondary">
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
                        CssClass="list-group"
                        Visible="false">
                    </asp:RadioButtonList>

                    <asp:TextBox
                        ID="txtAnswer"
                        runat="server"
                        CssClass="form-control mt-3"
                        TextMode="MultiLine"
                        Rows="4"
                        Visible="false" />

                </div>
            </asp:Panel>

        </div>

    </ItemTemplate>
</asp:Repeater>



make my design looks good. controls of carousel are not looking properly and height of module attachment is good but after the attachment quiz is looking low I want same height and looks the good 
<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">

<div class="container mt-4">

    <div id="quizCarousel" class="carousel slide" data-bs-interval="false">
        <div class="carousel-inner">

         <asp:Repeater ID="rptSlides"
    runat="server"
    OnItemDataBound="rptSlides_ItemDataBound">

    <ItemTemplate>

        <div class='carousel-item <%# Container.ItemIndex == 0 ? "active" : "" %>'>


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

            <asp:Panel runat="server"
                Visible='<%# Eval("SlideType").ToString() == "QUESTION" %>'>

                <div class="card shadow p-4">

                    <h6 class="text-muted">
                        <%# Eval("ModuleName") %>
                    </h6>

                    <h5 class="mb-3">
                        <%# Eval("Question") %>
                    </h5>

                    <asp:Image runat="server"
                        ID="imgQuestion"
                        CssClass="img-fluid mb-3"
                        Visible="false" />

                    <asp:RadioButtonList
                        ID="rblOptions"
                        runat="server"
                        CssClass="list-group"
                        Visible="false">
                    </asp:RadioButtonList>

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


    <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>

</asp:Content>
