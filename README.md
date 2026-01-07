
<script type="text/javascript">
    document.addEventListener("DOMContentLoaded", function () {

        const carouselEl = document.getElementById('quizCarousel');
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

        function lockPrev() {
            prevBtn.classList.add('disabled');
            prevBtn.style.pointerEvents = 'none';
            prevBtn.style.opacity = '0.4';
        }

        function lockNext() {
            nextBtn.classList.add('disabled');
            nextBtn.style.pointerEvents = 'none';
            nextBtn.style.opacity = '0.4';
        }

        function unlockNext() {
            nextBtn.classList.remove('disabled');
            nextBtn.style.pointerEvents = 'auto';
            nextBtn.style.opacity = '1';
        }

        

        setTimeout(() => {
            lockPrev(); 

            if (isQuestionSlide()) {
                lockNext();
            } else {
                unlockNext(); 
            }
        }, 100);

       

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

        unlockNext();
    });



    document.addEventListener('input', function (e) {

        if (!e.target.matches('textarea[data-question-type="subjective"]')) return;

        if (e.target.value.trim().length > 0) {
            unlockNext();
        } else {
            lockNext();
        }
    });


    carouselEl.addEventListener('slide.bs.carousel', function (e) {

        lockPrev(); 

        if (isQuestionSlide() && nextBtn.classList.contains('disabled')) {
            e.preventDefault();
        }
    });

   

    carouselEl.addEventListener('slid.bs.carousel', function (e) {

        lockPrev();

        const slides = carouselEl.querySelectorAll('.carousel-item');
        const prevSlide = slides[e.from];

      
        if (prevSlide) {
            const txt = prevSlide.querySelector('textarea[data-question-type="subjective"]');
            if (txt && !txt.classList.contains('locked')) {

                txt.classList.add('locked');
                txt.setAttribute('readonly', 'readonly');

                saveAnswer({
                    UserID: '<%= Session["UserName"] %>',
                    ModuleID: prevSlide.dataset.moduleid,
                    QuestionID: prevSlide.dataset.questionid,
                    SelectedOption: null,
                    Subjective_Answer: txt.value,
                    IsCorrect: false
                });
            }
        }

        if (isQuestionSlide()) {
            lockNext();
        } else {
            unlockNext(); 
        }
    });
});



    function saveAnswer(model) {
        fetch('QuestionResult.aspx/SaveAnswer', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ model: model })
        });
    }
</script>
