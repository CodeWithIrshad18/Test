<asp:HiddenField ID="hfResumeQuestionId" runat="server"
    Value='<%= ViewState["ResumeQuestionId"] %>' />

<asp:HiddenField ID="hfResumeModuleId" runat="server"
    Value='<%= ViewState["ResumeModuleId"] %>' />

<asp:HiddenField ID="hfQuizCompleted" runat="server"
    Value='<%= ViewState["QuizCompleted"] %>' />


<asp:HiddenField ID="hfResumeQuestionId" runat="server" />
<asp:HiddenField ID="hfResumeModuleId" runat="server" />
<asp:HiddenField ID="hfQuizCompleted" runat="server" />


const hfResumeQuestionId = document.getElementById('<%= hfResumeQuestionId.ClientID %>');
const hfResumeModuleId = document.getElementById('<%= hfResumeModuleId.ClientID %>');
const hfQuizCompleted = document.getElementById('<%= hfQuizCompleted.ClientID %>');

const resumeQuestionId = hfResumeQuestionId ? hfResumeQuestionId.value : "";
const resumeModuleId = hfResumeModuleId ? hfResumeModuleId.value : "";
const quizCompleted = hfQuizCompleted ? hfQuizCompleted.value === "true" : false;




why I am getting error , there is no error message is showing in think is It in aspx

<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">

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

    <div id="quizCarousel" class="carousel slide" data-bs-interval="false">
        <div class="carousel-inner">

        <asp:Repeater ID="rptSlides"
    runat="server"
    OnItemDataBound="rptSlides_ItemDataBound">

    <ItemTemplate>

     <div class='carousel-item 
    <%# Container.ItemIndex == 0 ? "active" : "" %>
    <%# Eval("SlideType").ToString() == "QUESTION" ? "quiz-slide" : "attachment-slide" %>'
    data-moduleid='<%# Eval("ModuleId") %>'
    data-questionid='<%# Eval("QuestionId") %>'>

           
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


            <asp:Panel runat="server"
                Visible='<%# Eval("SlideType").ToString() == "QUESTION" %>'>

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

                                    

             <asp:HiddenField ID="hfResumeModuleId" runat="server" />
    
        </div>

                     <asp:HiddenField ID="hfResumeQuestionId" runat="server"
    Value='<%# ViewState["ResumeQuestionId"] %>' />

<asp:HiddenField ID="hfQuizCompleted" runat="server"
    Value='<%# ViewState["QuizCompleted"] %>' />

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
<script type="text/javascript">
    document.addEventListener("DOMContentLoaded", function () {

        const hfResumeQuestionId = document.getElementById('<%= hfResumeQuestionId.ClientID %>');
    const hfResumeModuleId = document.getElementById('<%= hfResumeModuleId.ClientID %>');
    const hfQuizCompleted = document.getElementById('<%= hfQuizCompleted.ClientID %>');

    const resumeQuestionId = hfResumeQuestionId ? hfResumeQuestionId.value : "";
    const resumeModuleId = hfResumeModuleId ? hfResumeModuleId.value : "";
    const quizCompleted = hfQuizCompleted ? hfQuizCompleted.value === "true" : false;

    const carouselEl = document.getElementById('quizCarousel');
    const completedBox = document.getElementById('quizCompleted');
    const alreadyCompletedBox = document.getElementById('quizAlreadyCompleted');

    if (quizCompleted) {
        carouselEl.classList.add('d-none');
        alreadyCompletedBox.classList.remove('d-none');
        return;
    }

    const carousel = bootstrap.Carousel.getOrCreateInstance(carouselEl, {
        interval: false,
        ride: false,
        wrap: false
    });

    const nextBtn = document.querySelector('.carousel-control-next');
    const prevBtn = document.querySelector('.carousel-control-prev');

    function activeSlide() {
        return carouselEl.querySelector('.carousel-item.active');
    }

    function isQuestionSlide() {
        const slide = activeSlide();
        return slide && slide.classList.contains('quiz-slide');
    }

    function isLastSlide(slide) {
        const slides = carouselEl.querySelectorAll('.carousel-item');
        return slide === slides[slides.length - 1];
    }

    function lockPrev() {
        prevBtn.classList.add('disabled');
        prevBtn.style.pointerEvents = 'none';
        prevBtn.style.opacity = '0.3';
    }

    function lockNext() {
        nextBtn.classList.add('disabled');
        nextBtn.style.pointerEvents = 'none';
        nextBtn.style.opacity = '0.3';
    }

    function unlockNext() {
        nextBtn.classList.remove('disabled');
        nextBtn.style.pointerEvents = 'auto';
        nextBtn.style.opacity = '1';
    }

    function showCompletion() {
        carouselEl.classList.add('d-none');
        completedBox.classList.remove('d-none');
        nextBtn.style.display = 'none';
        prevBtn.style.display = 'none';
    }

    /* ========== INITIAL STATE ========== */
    setTimeout(() => {
        lockPrev();
        if (isQuestionSlide()) lockNext();
        else unlockNext();
    }, 100);

    /* ========== RESUME LOGIC (MODULE FIRST) ========== */
    if (resumeModuleId) {
        const slides = carouselEl.querySelectorAll('.carousel-item');
        let targetIndex = 0;

        slides.forEach((slide, index) => {
            if (
                slide.dataset.moduleid &&
                slide.dataset.moduleid.toLowerCase() === resumeModuleId.toLowerCase()
            ) {
                targetIndex = index;
            }
        });

        carousel.to(targetIndex);
    }

    /* ========== OBJECTIVE ========== */
    document.addEventListener('change', function (e) {

        if (!e.target.matches('.quiz-options input[type="radio"]')) return;

        const rbl = e.target.closest('.quiz-options');
        if (!rbl || rbl.classList.contains('locked')) return;

        const slide = rbl.closest('.quiz-slide');
        if (!slide) return;

        const selectedValue = e.target.value;
        const correctAns = rbl.getAttribute('data-answer');

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

        rbl.classList.add('locked');
        rbl.querySelectorAll('input').forEach(i => i.disabled = true);

        saveAnswer({
            UserID: '<%= Session["UserName"] %>',
            ModuleID: slide.dataset.moduleid,
            QuestionID: slide.dataset.questionid,
            SelectedOption: parseInt(selectedValue),
            Subjective_Answer: null,
            IsCorrect: selectedValue === correctAns
        });

        if (isLastSlide(slide)) {
            showCompletion();
        } else {
            unlockNext();
        }
    });

    /* ========== SUBJECTIVE VALIDATION ONLY ========== */
    document.addEventListener('input', function (e) {
        if (!e.target.matches('textarea[data-question-type="subjective"]')) return;

        if (e.target.value.trim().length > 0) unlockNext();
        else lockNext();
    });

    /* ========== SAVE SUBJECTIVE ON NEXT ========== */
    nextBtn.addEventListener('click', function (e) {

        const slide = activeSlide();
        if (!slide || !slide.classList.contains('quiz-slide')) return;

        const txt = slide.querySelector('textarea[data-question-type="subjective"]');
        if (!txt || txt.classList.contains('locked')) return;

        const value = txt.value.trim();
        if (value.length === 0) {
            e.preventDefault();
            lockNext();
            return;
        }

        saveAnswer({
            UserID: '<%= Session["UserName"] %>',
            ModuleID: slide.dataset.moduleid,
            QuestionID: slide.dataset.questionid,
            SelectedOption: null,
            Subjective_Answer: value,
            IsCorrect: false
        });

        txt.classList.add('locked');
        txt.setAttribute('readonly', 'readonly');

        if (isLastSlide(slide)) {
            e.preventDefault();
            showCompletion();
        }
    });

    /* ========== BLOCK INVALID SLIDES ========== */
    carouselEl.addEventListener('slide.bs.carousel', function (e) {
        lockPrev();
        if (isQuestionSlide() && nextBtn.classList.contains('disabled')) {
            e.preventDefault();
        }
    });

    carouselEl.addEventListener('slid.bs.carousel', function () {
        lockPrev();
        if (isQuestionSlide()) lockNext();
        else unlockNext();
    });

});

    /* ========== AJAX SAVE ========== */
    function saveAnswer(model) {
        fetch('QuestionResult.aspx/SaveAnswer', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ model: model })
        });
    }
</script>


</asp:Content>
