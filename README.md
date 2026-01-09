<!-- Stepper -->
<div id="quizStepper" class="stepper-container mb-3"></div>

.stepper-container {
    display: flex;
    justify-content: space-between;
    align-items: center;
    overflow-x: auto;
    padding: 5px 0;
}

.step {
    display: flex;
    flex-direction: column;
    align-items: center;
    flex: 1;
    position: relative;
}

.step::after {
    content: "";
    position: absolute;
    top: 12px;
    right: -50%;
    width: 100%;
    height: 2px;
    background: #dee2e6;
    z-index: 0;
}

.step:last-child::after {
    display: none;
}

.step-circle {
    width: 26px;
    height: 26px;
    border-radius: 50%;
    background: #dee2e6;
    color: #000;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 12px;
    z-index: 1;
}

.step.active .step-circle {
    background: #0d6efd;
    color: #fff;
}

.step.completed .step-circle {
    background: #198754;
    color: #fff;
}

.step-label {
    font-size: 10px;
    margin-top: 4px;
    text-align: center;
    white-space: nowrap;
}

const stepperContainer = document.getElementById("quizStepper");
const slides = carouselEl.querySelectorAll(".carousel-item");

function buildStepper() {
    stepperContainer.innerHTML = "";

    slides.forEach((slide, index) => {
        const step = document.createElement("div");
        step.className = "step";

        const circle = document.createElement("div");
        circle.className = "step-circle";
        circle.innerText = index + 1;

        const label = document.createElement("div");
        label.className = "step-label";
        label.innerText = slide.classList.contains("quiz-slide") ? "Q" : "M";

        step.appendChild(circle);
        step.appendChild(label);
        stepperContainer.appendChild(step);
    });
}

function updateStepper() {
    const activeIndex = [...slides].findIndex(s => s.classList.contains("active"));

    const steps = document.querySelectorAll(".step");

    steps.forEach((step, index) => {
        step.classList.remove("active", "completed");

        if (index < activeIndex) {
            step.classList.add("completed");
        }
        else if (index === activeIndex) {
            step.classList.add("active");
        }
    });
}

// Build once
buildStepper();

// Update on slide change
setTimeout(updateStepper, 300);

carouselEl.addEventListener("slid.bs.carousel", function () {
    updateStepper();
});


document.getElementById("quizStepper").style.display = "none";




<!-- Progress Container -->
<div class="mb-3">
    <div class="d-flex justify-content-between align-items-center mb-1">
        <small class="text-muted" id="progressText">0%</small>
        <small class="text-muted" id="slideCounter">0 / 0</small>
    </div>

    <div class="progress" style="height: 10px; border-radius: 20px;">
        <div id="quizProgressBar"
             class="progress-bar progress-bar-striped progress-bar-animated bg-success"
             role="progressbar"
             style="width: 0%; border-radius: 20px;">
        </div>
    </div>
</div>

<style>
    .progress {
        background: #e9ecef;
        overflow: hidden;
    }

    #quizProgressBar {
        transition: width 0.4s ease-in-out;
    }
</style>

const progressBar = document.getElementById("quizProgressBar");
const progressText = document.getElementById("progressText");
const slideCounter = document.getElementById("slideCounter");

const allSlides = carouselEl.querySelectorAll(".carousel-item");
const totalSlides = allSlides.length;

function updateProgress() {
    const activeIndex = [...allSlides].findIndex(s => s.classList.contains("active"));
    const current = activeIndex + 1;

    const percent = Math.round((current / totalSlides) * 100);

    progressBar.style.width = percent + "%";
    progressText.innerText = percent + "% completed";
    slideCounter.innerText = `${current} / ${totalSlides}`;
}

// Initial call
setTimeout(updateProgress, 300);

// On slide change
carouselEl.addEventListener("slid.bs.carousel", function () {
    updateProgress();
});


document.querySelector(".progress").style.display = "none";



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

