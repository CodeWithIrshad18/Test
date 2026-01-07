<div id="quizCompleted" class="text-center mt-5 d-none">
    <div class="card shadow p-5">
        <h3 class="text-success mb-3">🎉 Quiz Completed!</h3>
        <p class="text-muted">Thank you for completing the quiz.</p>
    </div>
</div>


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

    // SAVE IMMEDIATELY
    saveAnswer({
        ModuleID: slide.dataset.moduleid,
        QuestionID: slide.dataset.questionid,
        SelectedOption: parseInt(selectedValue),
        Subjective_Answer: null,
        IsCorrect: selectedValue === correctAns
    });

    // ✅ LAST SLIDE CHECK
    if (isLastSlide(slide)) {
        showCompletion();
    } else {
        unlockNext();
    }
});


document.addEventListener('input', function (e) {

    if (!e.target.matches('textarea[data-question-type="subjective"]')) return;

    const slide = e.target.closest('.quiz-slide');
    if (!slide) return;

    if (e.target.value.trim().length > 0) {
        unlockNext();

        // ✅ LAST SLIDE SUBJECTIVE SAVE
        if (isLastSlide(slide)) {
            saveAnswer({
                ModuleID: slide.dataset.moduleid,
                QuestionID: slide.dataset.questionid,
                SelectedOption: null,
                Subjective_Answer: e.target.value,
                IsCorrect: false
            });

            showCompletion();
        }

    } else {
        lockNext();
    }
});


function isLastSlide(slide) {
    const slides = document.querySelectorAll('#quizCarousel .carousel-item');
    return slide === slides[slides.length - 1];
}

function showCompletion() {

    // hide carousel
    document.getElementById('quizCarousel').classList.add('d-none');

    // show completion message
    document.getElementById('quizCompleted').classList.remove('d-none');

    // disable navigation
    document.querySelector('.carousel-control-next').style.display = 'none';
    document.querySelector('.carousel-control-prev').style.display = 'none';
}





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

    /* -------------------------------------------------- */
    /* HELPERS                                            */
    /* -------------------------------------------------- */

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

    /* -------------------------------------------------- */
    /* INITIAL STATE                                      */
    /* -------------------------------------------------- */

    setTimeout(() => {
        lockPrev(); // previous never allowed

        if (isQuestionSlide()) {
            lockNext();
        } else {
            unlockNext(); // attachment slide
        }
    }, 100);

    /* -------------------------------------------------- */
    /* OBJECTIVE QUESTIONS                                */
    /* -------------------------------------------------- */

    document.addEventListener('change', function (e) {

        if (!e.target.matches('.quiz-options input[type="radio"]')) return;

        const rbl = e.target.closest('.quiz-options');
        if (!rbl || rbl.classList.contains('locked')) return;

        const slide = rbl.closest('.quiz-slide');
        if (!slide) return;

        const selectedValue = e.target.value;
        const correctAns = rbl.getAttribute('data-answer');

        // reset visuals
        rbl.querySelectorAll('input').forEach(i => {
            i.classList.remove('correct', 'wrong');
        });

        // apply feedback
        if (selectedValue === correctAns) {
            e.target.classList.add('correct');
        } else {
            e.target.classList.add('wrong');
            const correctInput = rbl.querySelector(`input[value="${correctAns}"]`);
            if (correctInput) correctInput.classList.add('correct');
        }

        // lock options
        rbl.classList.add('locked');
        rbl.querySelectorAll('input').forEach(i => i.disabled = true);

        // save answer
        saveAnswer({
            UserID: '<%= Session["UserID"] %>',
            ModuleID: slide.dataset.moduleid,
            QuestionID: slide.dataset.questionid,
            SelectedOption: parseInt(selectedValue),
            Subjective_Answer: null,
            IsCorrect: selectedValue === correctAns
        });

        unlockNext();
    });

    /* -------------------------------------------------- */
    /* SUBJECTIVE QUESTIONS                               */
    /* -------------------------------------------------- */

    document.addEventListener('input', function (e) {

        if (!e.target.matches('textarea[data-question-type="subjective"]')) return;

        if (e.target.value.trim().length > 0) {
            unlockNext();
        } else {
            lockNext();
        }
    });

    /* -------------------------------------------------- */
    /* BLOCK INVALID SLIDE                                */
    /* -------------------------------------------------- */

    carouselEl.addEventListener('slide.bs.carousel', function (e) {

        lockPrev(); // previous always blocked

        if (isQuestionSlide() && nextBtn.classList.contains('disabled')) {
            e.preventDefault();
        }
    });

    /* -------------------------------------------------- */
    /* AFTER SLIDE CHANGE                                 */
    /* -------------------------------------------------- */

    carouselEl.addEventListener('slid.bs.carousel', function (e) {

        lockPrev();

        const slides = carouselEl.querySelectorAll('.carousel-item');
        const prevSlide = slides[e.from];

        // save + lock subjective AFTER leaving slide
        if (prevSlide) {
            const txt = prevSlide.querySelector('textarea[data-question-type="subjective"]');
            if (txt && !txt.classList.contains('locked')) {

                txt.classList.add('locked');
                txt.setAttribute('readonly', 'readonly');

                saveAnswer({
                    UserID: '<%= Session["UserID"] %>',
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
            unlockNext(); // attachment slide
        }
    });
});

/* -------------------------------------------------- */
/* AJAX SAVE                                           */
/* -------------------------------------------------- */

function saveAnswer(model) {
    fetch('QuestionResult.aspx/SaveAnswer', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ model: model })
    });
}
</script>








<div class='carousel-item 
    <%# Container.ItemIndex == 0 ? "active" : "" %>
    <%# Eval("SlideType").ToString() == "QUESTION" ? "quiz-slide" : "attachment-slide" %>'
    data-moduleid='<%# Eval("ModuleId") %>'
    data-questionid='<%# Eval("QuestionId") %>'>





Uncaught TypeError: Cannot read properties of null (reading 'value')
    at HTMLDivElement.<anonymous> (QuestionResult.aspx:1091:74)
    at Object.trigger (event-handler.js:289:15)
    at r (carousel.js:316:27)
    at carousel.js:362:7
    at g (index.js:226:51)
    at HTMLDivElement.a (index.js:247:5)


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


    <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
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
                unlockNext(); // attachment
            }
        }, 100);



        document.addEventListener('change', function (e) {

            if (!e.target.matches('.quiz-options input[type="radio"]')) return;

            const rbl = e.target.closest('.quiz-options');
            if (rbl.classList.contains('locked')) return;

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
                UserID: '<%= Session["UserID"] %>',
            ModuleID: rbl.closest('.quiz-slide').querySelector('[id$=hfModuleId]').value,
            QuestionID: rbl.closest('.quiz-slide').querySelector('[id$=hfQuestionId]').value,
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

        lockPrev(); // previous never allowed

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
                    UserID: '<%= Session["UserID"] %>',
                    ModuleID: prevSlide.querySelector('[id$=hfModuleId]').value,
                    QuestionID: prevSlide.querySelector('[id$=hfQuestionId]').value,
                    SelectedOption: null,
                    Subjective_Answer: txt.value,
                    IsCorrect: false
                });
            }
        }

        if (isQuestionSlide()) {
            lockNext();
        } else {
            unlockNext(); // attachment
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
</script></asp:Content>



        void LoadSlides()
        {
            DataTable dt = new DataTable();
            dt.Columns.Add("SlideType"); 
            dt.Columns.Add("ModuleId");
            dt.Columns.Add("ModuleName");
            dt.Columns.Add("Attachment");
            dt.Columns.Add("QuestionType");
            dt.Columns.Add("Question");
            dt.Columns.Add("Option1");
            dt.Columns.Add("Option2");
            dt.Columns.Add("Option3");
            dt.Columns.Add("Option4");
            dt.Columns.Add("QuestionImage");
            dt.Columns.Add("Ans");
            dt.Columns.Add("QuestionId");

            string cs = ConfigurationManager.ConnectionStrings["ApplicationServices"].ConnectionString;

            DataTable modules = new DataTable();
            DataTable attachments = new DataTable();
            DataTable questions = new DataTable();

            using (SqlConnection con = new SqlConnection(cs))
            {
                con.Open();


                new SqlDataAdapter(
                    "SELECT ID, ModuleName, SerialNo FROM App_Module_Master WHERE IsActive = 1 ORDER BY SerialNo",
                    con).Fill(modules);


                new SqlDataAdapter(
                    "SELECT ModuleID, Attachments, SeqNo FROM App_Module_Attachments WHERE IsActive = 1 ORDER BY ModuleID, SeqNo",
                    con).Fill(attachments);


                new SqlDataAdapter(
                    @"SELECT Id, ModuleID, QuestionType, Question,
                     Option1, Option2, Option3, Option4,Ans,
                     QuestionImage, SeqNo
              FROM App_QuestionMaster
              WHERE IsActive = 1
              ORDER BY ModuleID, SeqNo",
                    con).Fill(questions);
            }

            foreach (DataRow m in modules.Rows)
            {
                string moduleId = m["ID"].ToString();


                foreach (DataRow a in attachments.Select($"ModuleID = '{moduleId}'"))
                {
                    DataRow r = dt.NewRow();
                    r["SlideType"] = "ATTACHMENT";
                    r["ModuleId"] = moduleId;
                    r["ModuleName"] = m["ModuleName"];
                    r["Attachment"] = a["Attachments"];
                    dt.Rows.Add(r);
                }

                foreach (DataRow q in questions.Select($"ModuleID = '{moduleId}'"))
                {
                    DataRow r = dt.NewRow();
                    r["SlideType"] = "QUESTION";
                    r["ModuleId"] = moduleId;
                    r["ModuleName"] = m["ModuleName"];
                    r["QuestionType"] = q["QuestionType"];
                    r["Question"] = q["Question"];
                    r["Option1"] = q["Option1"];
                    r["Option2"] = q["Option2"];
                    r["Option3"] = q["Option3"];
                    r["Option4"] = q["Option4"];
                    r["QuestionImage"] = q["QuestionImage"];
                    r["Ans"] = q["Ans"];
                    r["QuestionId"] = q["Id"];
                    dt.Rows.Add(r);
                }
            }

            rptSlides.DataSource = dt;
            rptSlides.DataBind();


        }


        protected void rptSlides_ItemDataBound(object sender, RepeaterItemEventArgs e)
        {

            HiddenField hfModuleId = (HiddenField)e.Item.FindControl("hfModuleId");
            HiddenField hfQuestionId = (HiddenField)e.Item.FindControl("hfQuestionId");


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
            string correctAns = row["Ans"].ToString();
            hfQuestionId.Value = row["QuestionId"].ToString();

            if (qType == "Objective")
            {
                RadioButtonList rbl = (RadioButtonList)e.Item.FindControl("rblOptions");
                rbl.Visible = true;
                rbl.Items.Clear();

                rbl.Items.Add(new ListItem(row["Option1"].ToString(), "1"));
                rbl.Items.Add(new ListItem(row["Option2"].ToString(), "2"));
                rbl.Items.Add(new ListItem(row["Option3"].ToString(), "3"));
                rbl.Items.Add(new ListItem(row["Option4"].ToString(), "4"));

                rbl.Attributes["data-answer"] = correctAns;
                rbl.Attributes["data-question-type"] = "objective";



                rbl.RepeatLayout = RepeatLayout.Flow;

               

               

            }



            if (qType == "Subjective")
            {
                TextBox txt = (TextBox)e.Item.FindControl("txtAnswer");
                txt.Visible = true;

                txt.Attributes["data-question-type"] = "subjective";
            }
        }


        [WebMethod]
        public static void SaveAnswer(UserAnswer model)
        {
            string cs = ConfigurationManager.ConnectionStrings["ApplicationServices"].ConnectionString;

            using (SqlConnection con = new SqlConnection(cs))
            using (SqlCommand cmd = new SqlCommand(@"
IF EXISTS (
    SELECT 1 FROM ASP_User_Response 
    WHERE UserID = @UserID AND ModuleID = @ModuleID AND QuestionID = @QuestionID
)
BEGIN
    UPDATE ASP_User_Response
    SET SelectedOption = @SelectedOption,
        Subjective_Answer = @Subjective_Answer,
        IsCorrect = @IsCorrect,
        Answered_On = GETDATE()
    WHERE UserID = @UserID AND ModuleID = @ModuleID AND QuestionID = @QuestionID
END
ELSE
BEGIN
    INSERT INTO ASP_User_Response
    (ID, UserID, ModuleID, QuestionID, SelectedOption, Subjective_Answer,
     IsCorrect, Answered_On, Created_By, Created_On)
    VALUES
    (NEWID(), @UserID, @ModuleID, @QuestionID, @SelectedOption,
     @Subjective_Answer, @IsCorrect, GETDATE(), @UserID, GETDATE())
END
", con))
            {
                cmd.Parameters.AddWithValue("@UserID", model.UserID);
                cmd.Parameters.AddWithValue("@ModuleID", model.ModuleID);
                cmd.Parameters.AddWithValue("@QuestionID", model.QuestionID);
                cmd.Parameters.AddWithValue("@SelectedOption", (object)model.SelectedOption ?? DBNull.Value);
                cmd.Parameters.AddWithValue("@Subjective_Answer", (object)model.Subjective_Answer ?? DBNull.Value);
                cmd.Parameters.AddWithValue("@IsCorrect", model.IsCorrect);

                con.Open();
                cmd.ExecuteNonQuery();
            }
        }

        public class UserAnswer
        {
            public int UserID { get; set; }
            public Guid ModuleID { get; set; }
            public Guid QuestionID { get; set; }
            public int? SelectedOption { get; set; }
            public string Subjective_Answer { get; set; }
            public bool IsCorrect { get; set; }
        }
