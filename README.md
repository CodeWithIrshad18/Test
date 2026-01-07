carousel.addEventListener('slid.bs.carousel', function (e) {

    lockPrev();

    // ✅ LOCK SUBJECTIVE OF PREVIOUS SLIDE ONLY
    const slides = carousel.querySelectorAll('.carousel-item');
    const prevSlide = slides[e.from];

    if (prevSlide) {
        const txt = prevSlide.querySelector(
            'textarea[data-question-type="subjective"]'
        );

        if (txt && !txt.classList.contains('locked')) {
            txt.classList.add('locked');
            txt.setAttribute('readonly', 'readonly');
        }
    }

    // Handle current slide navigation
    if (isQuestionSlide()) {
        lockNext();
    } else {
        unlockNext(); // attachment slide
    }
});

document.addEventListener('input', function (e) {

    if (!e.target.matches('textarea[data-question-type="subjective"]')) return;

    if (e.target.value.trim().length > 0) {
        unlockNext();
    } else {
        lockNext();
    }
});





document.addEventListener('input', function (e) {

    if (!e.target.matches('textarea[data-question-type="subjective"]')) return;

    const txt = e.target;

    if (txt.value.trim().length > 0) {
        unlockNext();   // allow moving forward
    } else {
        lockNext();     // block next if empty
    }
});



carousel.addEventListener('slid.bs.carousel', function () {

    lockPrev();

    const prevSlide = carousel.querySelector('.carousel-item:not(.active) textarea[data-question-type="subjective"]:not(.locked)');
    if (prevSlide) {
        prevSlide.classList.add('locked');
        prevSlide.setAttribute('readonly', 'readonly');
    }

    if (isQuestionSlide()) {
        lockNext();
    } else {
        unlockNext(); // attachment slide
    }
});



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

    function lockPrev() {
        prevBtn.classList.add('disabled');
        prevBtn.style.pointerEvents = 'none';
        prevBtn.style.opacity = '0.4';
    }

    function unlockNext() {
        nextBtn.classList.remove('disabled');
        nextBtn.style.pointerEvents = 'auto';
        nextBtn.style.opacity = '1';
    }

    function lockNext() {
        nextBtn.classList.add('disabled');
        nextBtn.style.pointerEvents = 'none';
        nextBtn.style.opacity = '0.4';
    }

    // INITIAL STATE
    setTimeout(() => {
        lockPrev();

        if (isQuestionSlide()) {
            lockNext();
        } else {
            unlockNext(); // attachment
        }
    }, 100);

    // OBJECTIVE ANSWER
    document.addEventListener('change', function (e) {

        if (!e.target.matches('.quiz-options input[type="radio"]')) return;

        const rbl = e.target.closest('.quiz-options');
        if (rbl.classList.contains('locked')) return;

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

        // lock objective
        rbl.classList.add('locked');
        rbl.querySelectorAll('input').forEach(i => i.disabled = true);

        unlockNext();
    });

    // SUBJECTIVE ANSWER
    document.addEventListener('input', function (e) {

        if (!e.target.matches('textarea[data-question-type="subjective"]')) return;

        const txt = e.target;
        if (txt.classList.contains('locked')) return;

        if (txt.value.trim().length > 0) {
            txt.classList.add('locked');
            txt.setAttribute('readonly', 'readonly');
            unlockNext();
        } else {
            lockNext();
        }
    });

    // BLOCK INVALID SLIDE
    carousel.addEventListener('slide.bs.carousel', function (e) {

        lockPrev(); // previous NEVER allowed

        if (isQuestionSlide() && nextBtn.classList.contains('disabled')) {
            e.preventDefault();
        }
    });

    // ON SLIDE CHANGE
    carousel.addEventListener('slid.bs.carousel', function () {

        lockPrev();

        if (isQuestionSlide()) {
            lockNext();
        } else {
            unlockNext(); // attachment always allowed
        }
    });

});
</script>





<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">

<div class="container mt-4">

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

</div>


    <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
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

        setTimeout(() => {
            disableAllNav();

            
            if (!isQuestionSlide()) {
                enableNextOnly();
            }
        }, 100);

 
        document.addEventListener('change', function (e) {

            if (!e.target.matches('.quiz-options input[type="radio"]')) return;

            const rbl = e.target.closest('.quiz-options');
            if (rbl.classList.contains('locked')) return;

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

      
            rbl.classList.add('locked');
            rbl.querySelectorAll('input').forEach(i => i.disabled = true);

            enableNextOnly();
        });

  
        document.addEventListener('input', function (e) {

            if (!e.target.matches('textarea[data-question-type="subjective"]')) return;

            const txt = e.target;
            if (txt.classList.contains('locked')) return;

            if (txt.value.trim().length > 0) {
                txt.classList.add('locked');
                txt.setAttribute('readonly', 'readonly');
                enableNextOnly();
            } else {
                disableAllNav();
            }
        });

        carousel.addEventListener('slide.bs.carousel', function (e) {
            if (prevBtn.classList.contains('disabled')) {
                e.preventDefault();
                return;
            }

            if (isQuestionSlide() && nextBtn.classList.contains('disabled')) {
                e.preventDefault();
            }
        });

        
        carousel.addEventListener('slid.bs.carousel', function () {

            disableAllNav();

            if (!isQuestionSlide()) {
              
                enableNextOnly();
            }
        });

    });

    function disableAllNav() {
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


        prevBtn.classList.add('disabled');
        prevBtn.style.pointerEvents = 'none';
        prevBtn.style.opacity = '0.4';
    }

</script>
</asp:Content>



error : 

Uncaught ReferenceError: nextBtn is not defined
    at disableAllNav (QuestionResult.aspx:966:9)
    at QuestionResult.aspx:886:13Understand this error
QuestionResult.aspx:966 Uncaught ReferenceError: nextBtn is not defined
    at disableAllNav (QuestionResult.aspx:966:9)
    at HTMLDivElement.<anonymous> (QuestionResult.aspx:955:13)
    at Object.trigger (event-handler.js:289:15)
    at r (carousel.js:316:27)
    at carousel.js:362:7
    at g (index.js:226:51)
    at HTMLDivElement.a (index.js:247:5)
