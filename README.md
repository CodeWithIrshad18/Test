CREATE TRIGGER Insurance_Renewal_RefNo
ON App_Insurance_Details
AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    -- Only when Renew Flag true ho (agar aapka column hai RenewFlag type ka)
    IF UPDATE(RenewFlag)
    BEGIN
        
        DECLARE @NewRef VARCHAR(255),
                @Id INT

        SELECT @Id = ID FROM Inserted

        -- Purana Ref OldRef me daal do
        UPDATE A
        SET OldRef = A.RefNo
        FROM App_Insurance_Details A
        INNER JOIN Inserted I ON A.ID = I.ID


        ---- NEW REF NO GENERATE KARO
        EXEC dbo.GetAutoGenNumberSimple
            @p1 = 'Insurance_Ref',
            @OutPut = @NewRef OUTPUT


        ---- AB REFNO UPDATE KAR DO
        UPDATE App_Insurance_Details
        SET RefNo = @NewRef
        WHERE ID = @Id
    END
END




SELECT Id, ModuleID, QuestionType, Question,
       Option1, Option2, Option3, Option4,
       Ans, QuestionImage, SeqNo


r["QuestionImage"] = q["QuestionImage"];
r["Ans"] = q["Ans"];   // ✅ ADD THIS




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





error :

Ans is neither a DataColumn nor a DataRelation for table .
Line 127:             string correctAns = row["Ans"].ToString();


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
             Option1, Option2, Option3, Option4,
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
    }
}
