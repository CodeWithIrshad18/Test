        protected void Page_Load(object sender, EventArgs e)
        {
            if (!IsPostBack)
                LoadSlides();
        }


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

        function isLastSlide(slide) {
            const slides = carouselEl.querySelectorAll('.carousel-item');
            return slide === slides[slides.length - 1];
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

        function showCompletion() {
            document.getElementById('quizCarousel').classList.add('d-none');
            document.getElementById('quizCompleted').classList.remove('d-none');

            nextBtn.style.display = 'none';
            prevBtn.style.display = 'none';
        }

        /* ---------------- INITIAL STATE ---------------- */

        setTimeout(() => {
            lockPrev();

            if (isQuestionSlide()) {
                lockNext();
            } else {
                unlockNext(); // attachment slide
            }
        }, 100);

        /* ---------------- OBJECTIVE QUESTIONS ---------------- */

        document.addEventListener('change', function (e) {

            if (!e.target.matches('.quiz-options input[type="radio"]')) return;

            const rbl = e.target.closest('.quiz-options');
            if (!rbl || rbl.classList.contains('locked')) return;

            const slide = rbl.closest('.quiz-slide');
            if (!slide) return;

            const selectedValue = e.target.value;
            const correctAns = rbl.getAttribute('data-answer');

            // reset styles
            rbl.querySelectorAll('input').forEach(i => {
                i.classList.remove('correct', 'wrong');
            });

            // feedback
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

            // save objective immediately
            saveAnswer({
                UserID: '<%= Session["UserName"] %>',
            ModuleID: slide.dataset.moduleid,
            QuestionID: slide.dataset.questionid,
            SelectedOption: parseInt(selectedValue),
            Subjective_Answer: null,
            IsCorrect: selectedValue === correctAns
        });

        // last slide?
        if (isLastSlide(slide)) {
            showCompletion();
        } else {
            unlockNext();
        }
    });

    /* ---------------- SUBJECTIVE VALIDATION ONLY ---------------- */

    document.addEventListener('input', function (e) {

        if (!e.target.matches('textarea[data-question-type="subjective"]')) return;

        if (e.target.value.trim().length > 0) {
            unlockNext();
        } else {
            lockNext();
        }
    });



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

        // save subjective on NEXT
        saveAnswer({
            UserID: '<%= Session["UserName"] %>',
            ModuleID: slide.dataset.moduleid,
            QuestionID: slide.dataset.questionid,
            SelectedOption: null,
            Subjective_Answer: value,
            IsCorrect: false
        });

        // lock after save
        txt.classList.add('locked');
        txt.setAttribute('readonly', 'readonly');

        // last slide?
        if (isLastSlide(slide)) {
            e.preventDefault();
            showCompletion();
        }
    });

    /* ---------------- BLOCK INVALID SLIDE ---------------- */

    carouselEl.addEventListener('slide.bs.carousel', function (e) {

        lockPrev();

        if (isQuestionSlide() && nextBtn.classList.contains('disabled')) {
            e.preventDefault();
        }
    });

    /* ---------------- AFTER SLIDE CHANGE ---------------- */

    carouselEl.addEventListener('slid.bs.carousel', function () {

        lockPrev();

        if (isQuestionSlide()) {
            lockNext();
        } else {
            unlockNext(); // attachment slide
        }
    });
});

    /* ---------------- AJAX SAVE ---------------- */

    function saveAnswer(model) {
        fetch('QuestionResult.aspx/SaveAnswer', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ model: model })
        });
    }
</script>
