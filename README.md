<div class='carousel-item 
    <%# Container.ItemIndex == 0 ? "active" : "" %>
    <%# Eval("SlideType").ToString() == "QUESTION" ? "quiz-slide" : "attachment-slide" %>'>

<script>
document.addEventListener("DOMContentLoaded", function () {

    const carousel = document.getElementById('quizCarousel');
    const nextBtn = document.querySelector('.carousel-control-next');

    function isQuestionSlide() {
        const active = carousel.querySelector('.carousel-item.active');
        return active && active.classList.contains('quiz-slide');
    }

    function disableNext() {
        nextBtn.classList.add('disabled');
        nextBtn.style.pointerEvents = 'none';
        nextBtn.style.opacity = '0.4';
    }

    function enableNext() {
        nextBtn.classList.remove('disabled');
        nextBtn.style.pointerEvents = 'auto';
        nextBtn.style.opacity = '1';
    }

    // Initial state
    setTimeout(() => {
        if (isQuestionSlide()) {
            disableNext();
        } else {
            enableNext(); // attachment slide
        }
    }, 100);

    // Objective option selected
    document.addEventListener('change', function (e) {

        if (!e.target.matches('.quiz-options input[type="radio"]')) return;

        const rbl = e.target.closest('.quiz-options');
        const correctAns = rbl.getAttribute('data-answer');
        const selectedValue = e.target.value;

        // Reset styles
        rbl.querySelectorAll('input').forEach(i => {
            i.classList.remove('correct', 'wrong');
        });

        if (selectedValue === correctAns) {
            e.target.classList.add('correct');
        } else {
            e.target.classList.add('wrong');

            const correctInput = rbl.querySelector(`input[value="${correctAns}"]`);
            if (correctInput) correctInput.classList.add('correct');
        }

        enableNext();
    });

    // Subjective validation
    document.addEventListener('input', function (e) {

        if (!e.target.matches('textarea[data-question-type="subjective"]')) return;

        if (e.target.value.trim().length > 0) {
            enableNext();
        } else {
            disableNext();
        }
    });

    // Prevent sliding if question not answered
    carousel.addEventListener('slide.bs.carousel', function (e) {
        if (isQuestionSlide() && nextBtn.classList.contains('disabled')) {
            e.preventDefault();
        }
    });

    // On slide change
    carousel.addEventListener('slid.bs.carousel', function () {
        if (isQuestionSlide()) {
            disableNext();
        } else {
            enableNext(); // attachment slide
        }
    });

});
</script>




string correctAns = row["Ans"].ToString();   // Ans column (1–4)
rbl.Attributes["data-answer"] = correctAns;
rbl.Attributes["data-question-type"] = "objective";

/* Correct option */
.quiz-options input[type="radio"].correct + label {
    border-color: #198754 !important;
    background-color: #e9f7ef !important;
    color: #198754;
}

/* Wrong option */
.quiz-options input[type="radio"].wrong + label {
    border-color: #dc3545 !important;
    background-color: #fdecea !important;
    color: #dc3545;
}

<script>
document.addEventListener("DOMContentLoaded", function () {

    const carousel = document.querySelector('#quizCarousel');
    const nextBtn = document.querySelector('.carousel-control-next');

    // Disable next initially
    disableNext();

    // Objective option click
    document.addEventListener('change', function (e) {

        if (!e.target.matches('.quiz-options input[type="radio"]')) return;

        const rbl = e.target.closest('.quiz-options');
        const correctAns = rbl.getAttribute('data-answer');
        const selectedValue = e.target.value;

        // Reset styles
        rbl.querySelectorAll('input').forEach(i => {
            i.classList.remove('correct', 'wrong');
        });

        // Mark selected
        if (selectedValue === correctAns) {
            e.target.classList.add('correct');
        } else {
            e.target.classList.add('wrong');

            // Highlight correct one
            const correctInput = rbl.querySelector(`input[value="${correctAns}"]`);
            if (correctInput) correctInput.classList.add('correct');
        }

        enableNext();
    });

    // Subjective validation
    document.addEventListener('input', function (e) {
        if (!e.target.matches('textarea[data-question-type="subjective"]')) return;

        if (e.target.value.trim().length > 0) {
            enableNext();
        } else {
            disableNext();
        }
    });

    // Prevent sliding if invalid
    carousel.addEventListener('slide.bs.carousel', function (e) {
        if (nextBtn.classList.contains('disabled')) {
            e.preventDefault();
        }
    });

    function disableNext() {
        nextBtn.classList.add('disabled');
        nextBtn.style.pointerEvents = 'none';
        nextBtn.style.opacity = '0.4';
    }

    function enableNext() {
        nextBtn.classList.remove('disabled');
        nextBtn.style.pointerEvents = 'auto';
        nextBtn.style.opacity = '1';
    }

    // Reset next on slide change
    carousel.addEventListener('slid.bs.carousel', function () {
        disableNext();
    });

});
</script>




<asp:RadioButtonList
    ID="rblOptions"
    runat="server"
    CssClass="quiz-options"
    RepeatDirection="Vertical"
    Visible="false">
</asp:RadioButtonList>

rbl.Visible = true;
rbl.Items.Clear();

rbl.Items.Add(new ListItem(row["Option1"].ToString(), "1"));
rbl.Items.Add(new ListItem(row["Option2"].ToString(), "2"));
rbl.Items.Add(new ListItem(row["Option3"].ToString(), "3"));
rbl.Items.Add(new ListItem(row["Option4"].ToString(), "4"));

rbl.RepeatLayout = RepeatLayout.Flow;

/* Container */
.quiz-options {
    margin-top: 20px;
}

/* Hide actual radio button */
.quiz-options input[type="radio"] {
    display: none;
}

/* Option card (label wraps input + text) */
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

/* Hover effect */
.quiz-options label:hover {
    border-color: #0d6efd;
    background-color: #f8f9fa;
}

/* Selected option */
.quiz-options input[type="radio"]:checked + label {
    border-color: #0d6efd;
    background-color: #e7f1ff;
    box-shadow: 0 0 0 2px rgba(13,110,253,0.15);
    font-weight: 600;
}

/* Smooth animation */
.quiz-options label {
    animation: fadeIn 0.2s ease;
}

@keyframes fadeIn {
    from { opacity: 0; transform: translateY(3px); }
    to { opacity: 1; transform: translateY(0); }
}

/* Mobile */
@media (max-width: 768px) {
    .quiz-options label {
        font-size: 15px;
        padding: 14px;
    }
}





<asp:RadioButtonList
    ID="rblOptions"
    runat="server"
    CssClass="quiz-options"
    RepeatDirection="Vertical"
    Visible="false">
</asp:RadioButtonList>

/* Quiz options container */
.quiz-options {
    margin-top: 15px;
}

/* Each option */
.quiz-options label {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 14px 16px;
    margin-bottom: 12px;
    border: 1px solid #dee2e6;
    border-radius: 10px;
    cursor: pointer;
    transition: all 0.25s ease;
    background-color: #fff;
    font-size: 16px;
}

/* Hide default radio */
.quiz-options input[type="radio"] {
    appearance: none;
    -webkit-appearance: none;
    width: 20px;
    height: 20px;
    border: 2px solid #adb5bd;
    border-radius: 50%;
    position: relative;
    cursor: pointer;
}

/* Checked state */
.quiz-options input[type="radio"]:checked {
    border-color: #0d6efd;
}

/* Inner dot */
.quiz-options input[type="radio"]:checked::after {
    content: "";
    width: 10px;
    height: 10px;
    background-color: #0d6efd;
    border-radius: 50%;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

/* Hover effect */
.quiz-options label:hover {
    background-color: #f8f9fa;
    border-color: #0d6efd;
}

/* Selected option highlight */
.quiz-options input[type="radio"]:checked + span {
    font-weight: 600;
    color: #0d6efd;
}

/* Option text */
.quiz-options span {
    flex: 1;
}

/* Mobile */
@media (max-width: 768px) {
    .quiz-options label {
        font-size: 15px;
        padding: 12px;
    }
}

rbl.Items.Add(new ListItem($"<span>{row["Option1"]}</span>", "1"));
rbl.Items.Add(new ListItem($"<span>{row["Option2"]}</span>", "2"));
rbl.Items.Add(new ListItem($"<span>{row["Option3"]}</span>", "3"));
rbl.Items.Add(new ListItem($"<span>{row["Option4"]}</span>", "4"));

rbl.DataBind();

rbl.Items[0].Attributes["class"] = "quiz-option";
rbl.Items[1].Attributes["class"] = "quiz-option";
rbl.Items[2].Attributes["class"] = "quiz-option";
rbl.Items[3].Attributes["class"] = "quiz-option";

rbl.RepeatLayout = RepeatLayout.Flow;

rbl.TextAlign = TextAlign.Right;




#quizCarousel {
    max-width: 900px;
    margin: auto;
}

.carousel-item {
    min-height: 520px;
}

/* Prevent overlap */
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
    max-height: 360px;
    object-fit: contain;
}

/* Controls */
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
    padding: 14px;
}

/* Mobile */
@media (max-width: 768px) {
    .carousel-inner {
        padding-left: 50px;
        padding-right: 50px;
    }

    .quiz-card {
        min-height: 420px;
    }
}







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
