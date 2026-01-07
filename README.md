
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

    /* ---------------- NAV CONTROL ---------------- */

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

    /* ---------------- INITIAL STATE ---------------- */

    setTimeout(() => {
        lockPrev();

        if (isQuestionSlide()) {
            lockNext();
        } else {
            unlockNext(); // attachment
        }
    }, 100);

    /* ---------------- OBJECTIVE ---------------- */

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

        // lock objective
        rbl.classList.add('locked');
        rbl.querySelectorAll('input').forEach(i => i.disabled = true);

        // save objective
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

    /* ---------------- SUBJECTIVE ---------------- */

    document.addEventListener('input', function (e) {

        if (!e.target.matches('textarea[data-question-type="subjective"]')) return;

        if (e.target.value.trim().length > 0) {
            unlockNext();
        } else {
            lockNext();
        }
    });

    /* ---------------- BLOCK INVALID SLIDE ---------------- */

    carouselEl.addEventListener('slide.bs.carousel', function (e) {

        lockPrev(); // previous never allowed

        if (isQuestionSlide() && nextBtn.classList.contains('disabled')) {
            e.preventDefault();
        }
    });

    /* ---------------- ON SLIDE CHANGE ---------------- */

    carouselEl.addEventListener('slid.bs.carousel', function (e) {

        lockPrev();

        const slides = carouselEl.querySelectorAll('.carousel-item');
        const prevSlide = slides[e.from];

        // lock & save subjective AFTER leaving slide
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

/* ---------------- AJAX SAVE ---------------- */

function saveAnswer(model) {
    fetch('QuestionResult.aspx/SaveAnswer', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ model: model })
    });
}
</script>




<asp:HiddenField ID="hfModuleId" runat="server" />
<asp:HiddenField ID="hfQuestionId" runat="server" />

HiddenField hfModuleId = (HiddenField)e.Item.FindControl("hfModuleId");
HiddenField hfQuestionId = (HiddenField)e.Item.FindControl("hfQuestionId");

hfModuleId.Value = row["ModuleId"].ToString();
hfQuestionId.Value = row["QuestionId"].ToString(); // ADD QuestionId to dt

dt.Columns.Add("QuestionId");
r["QuestionId"] = q["Id"];

<script>
function saveAnswer(data) {
    fetch('QuestionResult.aspx/SaveAnswer', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ model: data })
    });
}
</script>

saveAnswer({
    UserID: '<%= Session["UserID"] %>',
    ModuleID: rbl.closest('.quiz-slide').querySelector('[id$=hfModuleId]').value,
    QuestionID: rbl.closest('.quiz-slide').querySelector('[id$=hfQuestionId]').value,
    SelectedOption: selectedValue,
    Subjective_Answer: null,
    IsCorrect: selectedValue === correctAns
});

const txt = prevSlide.querySelector('textarea[data-question-type="subjective"]');
if (txt) {
    saveAnswer({
        UserID: '<%= Session["UserID"] %>',
        ModuleID: prevSlide.querySelector('[id$=hfModuleId]').value,
        QuestionID: prevSlide.querySelector('[id$=hfQuestionId]').value,
        SelectedOption: null,
        Subjective_Answer: txt.value,
        IsCorrect: false
    });
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

SELECT TOP 1 Q.Id
FROM App_QuestionMaster Q
LEFT JOIN ASP_User_Response R
    ON R.QuestionID = Q.Id AND R.UserID = @UserID
WHERE Q.ModuleID = @ModuleID
  AND R.QuestionID IS NULL
ORDER BY Q.SeqNo

string resumeQuestionId = GetFirstUnansweredQuestion(userId, moduleId);
ViewState["ResumeQuestionId"] = resumeQuestionId;


const resumeId = '<%= ViewState["ResumeQuestionId"] %>';
if (resumeId) {
    const slide = document.querySelector(`[data-question-id="${resumeId}"]`);
    if (slide) {
        bootstrap.Carousel.getInstance(document.getElementById('quizCarousel'))
            .to([...slide.parentNode.children].indexOf(slide));
    }
}




ID	UserID	ModuleID	QuestionID	SelectedOption	Subjective_Answer	IsCorrect	Answered_On	Created_By	Created_On
917C4B16-D20E-4BAA-AFD7-0F3DEFC1072B	159445	12ED0FE6-D3D3-4A13-BCCB-6E7272FDADF7	C7124DD4-AAEE-4348-9F9B-93DD4B202E97	NULL	swss	0	NULL	159445	2026-01-06 18:05:24.323
79FB73C1-8C63-4A50-BB86-D858F757E2DB	159445	12ED0FE6-D3D3-4A13-BCCB-6E7272FDADF7	C2BB258D-3FE3-4195-91DE-82370D99CC03	NULL	DALMA	0	NULL	159445	2025-12-29 17:46:56.977
F03FC5F2-B9EA-4E3C-B7E4-FF1A0F6D69AB	159445	041AC05D-822D-4597-9F89-C0389A56AD13	57FF2291-5528-40F8-92CB-060575E54ACF	3		0	NULL	159445	2026-01-06 18:05:55.120


CREATE TABLE [dbo].[ASP_User_Response] (
    [ID]                UNIQUEIDENTIFIER NOT NULL,
    [UserID]            INT              NULL,
    [ModuleID]          UNIQUEIDENTIFIER NULL,
    [QuestionID]        UNIQUEIDENTIFIER NULL,
    [SelectedOption]    INT              NULL,
    [Subjective_Answer] VARCHAR (MAX)    NULL,
    [IsCorrect]         BIT              NULL,
    [Answered_On]       DATETIME         NULL,
    [Created_By]        INT              NULL,
    [Created_On]        DATETIME         NULL,
    PRIMARY KEY CLUSTERED ([ID] ASC),
    CONSTRAINT [UQ_ASP_User_Response_ModuleID_QuestionID] UNIQUE NONCLUSTERED ([ModuleID] ASC, [QuestionID] ASC)
);



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
                 dt.Rows.Add(r);
             }
         }

         rptSlides.DataSource = dt;
         rptSlides.DataBind();


     }


     protected void rptSlides_ItemDataBound(object sender, RepeaterItemEventArgs e)
     {
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
