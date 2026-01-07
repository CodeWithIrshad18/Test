/* Lock state */
.locked {
    pointer-events: none;
    opacity: 0.8;
}

<script type="text/javascript">
document.addEventListener("DOMContentLoaded", function () {

    const carousel = document.getElementById('quizCarousel');
    const nextBtn = document.querySelector('.carousel-control-next');
    const prevBtn = document.querySelector('.carousel-control-prev');

    function activeSlide() {
        return carousel.querySelector('.carousel-item.active');
    }

    function isQuestionSlide() {
        const slide = activeSlide();
        return slide && slide.classList.contains('quiz-slide');
    }

    function disableNav() {
        nextBtn.classList.add('disabled');
        prevBtn.classList.add('disabled');
        nextBtn.style.pointerEvents = 'none';
        prevBtn.style.pointerEvents = 'none';
        nextBtn.style.opacity = '0.4';
        prevBtn.style.opacity = '0.4';
    }

    function enableNextOnly() {
        nextBtn.classList.remove('disabled');
        nextBtn.style.pointerEvents = 'auto';
        nextBtn.style.opacity = '1';

        // ❌ previous always locked for quiz slides
        prevBtn.classList.add('disabled');
        prevBtn.style.pointerEvents = 'none';
        prevBtn.style.opacity = '0.4';
    }

    function enableAllNav() {
        nextBtn.classList.remove('disabled');
        prevBtn.classList.remove('disabled');
        nextBtn.style.pointerEvents = 'auto';
        prevBtn.style.pointerEvents = 'auto';
        nextBtn.style.opacity = '1';
        prevBtn.style.opacity = '1';
    }

    // INITIAL STATE
    setTimeout(() => {
        if (isQuestionSlide()) {
            disableNav();
        } else {
            enableAllNav();
        }
    }, 100);

    // OBJECTIVE SELECTION
    document.addEventListener('change', function (e) {

        if (!e.target.matches('.quiz-options input[type="radio"]')) return;

        const rbl = e.target.closest('.quiz-options');

        // 🔒 Prevent changing answer
        if (rbl.classList.contains('locked')) return;

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

        // 🔒 Lock options after first choice
        rbl.classList.add('locked');
        rbl.querySelectorAll('input').forEach(i => i.disabled = true);

        enableNextOnly();
    });

    // SUBJECTIVE INPUT
    document.addEventListener('input', function (e) {

        if (!e.target.matches('textarea[data-question-type="subjective"]')) return;

        const txt = e.target;

        // 🔒 Prevent editing after first valid entry
        if (txt.classList.contains('locked')) return;

        if (txt.value.trim().length > 0) {
            txt.classList.add('locked');
            txt.setAttribute('readonly', 'readonly');
            enableNextOnly();
        } else {
            disableNav();
        }
    });

    // BLOCK SLIDE IF NOT ALLOWED
    carousel.addEventListener('slide.bs.carousel', function (e) {
        if (isQuestionSlide() && nextBtn.classList.contains('disabled')) {
            e.preventDefault();
        }
    });

    // ON SLIDE CHANGE
    carousel.addEventListener('slid.bs.carousel', function () {

        const slide = activeSlide();

        if (slide && slide.classList.contains('quiz-slide')) {
            disableNav();
        } else {
            enableAllNav();
        }
    });

});
</script>

    
    
    
    <div id="quizCarousel" class="carousel slide" data-bs-interval="false">
        <div class="carousel-inner">

        <asp:Repeater ID="rptSlides"
    runat="server"
    OnItemDataBound="rptSlides_ItemDataBound">

    <ItemTemplate>

      <div class='carousel-item 
    <%# Container.ItemIndex == 0 ? "active" : "" %>
    <%# Eval("SlideType").ToString() == "QUESTION" ? "quiz-slide" : "attachment-slide" %>'>

           
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


<script type="text/javascript">
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


        setTimeout(() => {
            if (isQuestionSlide()) {
                disableNext();
            } else {
                enableNext(); 
            }
        }, 100);


        document.addEventListener('change', function (e) {

            if (!e.target.matches('.quiz-options input[type="radio"]')) return;

            const rbl = e.target.closest('.quiz-options');
            const correctAns = rbl.getAttribute('data-answer');
            const selectedValue = e.target.value;

    
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

   
        document.addEventListener('input', function (e) {

            if (!e.target.matches('textarea[data-question-type="subjective"]')) return;

            if (e.target.value.trim().length > 0) {
                enableNext();
            } else {
                disableNext();
            }
        });


        carousel.addEventListener('slide.bs.carousel', function (e) {
            if (isQuestionSlide() && nextBtn.classList.contains('disabled')) {
                e.preventDefault();
            }
        });


        carousel.addEventListener('slid.bs.carousel', function () {
            if (isQuestionSlide()) {
                disableNext();
            } else {
                enableNext(); 
            }
        });

    });
</script>
