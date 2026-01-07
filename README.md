CREATE PROCEDURE USP_Generate_RefNo
(
    @Type VARCHAR(20),       -- REQUEST / RENEWAL
    @OldRefNo VARCHAR(50) = NULL,
    @NewRefNo VARCHAR(50) OUTPUT
)
AS
BEGIN
    DECLARE @Prefix VARCHAR(10) = 'REF';
    DECLARE @Seq INT;

    IF(@Type = 'REQUEST')
    BEGIN
        SELECT @Seq = ISNULL(MAX(CAST(RIGHT(RefNo,5) AS INT)),0) + 1
        FROM App_Insurance_Details
        WHERE RefType='REQUEST';

        SET @NewRefNo = @Prefix + RIGHT('00000' + CAST(@Seq AS VARCHAR),5);
    END
    ELSE IF(@Type='RENEWAL')
    BEGIN
        SELECT @Seq = ISNULL(MAX(CAST(RIGHT(RefNo,5) AS INT)),0) + 1
        FROM App_Insurance_Details
        WHERE RefType='RENEWAL';

        SET @NewRefNo = @Prefix + RIGHT('00000' + CAST(@Seq AS VARCHAR),5);
    END
END



CREATE TRIGGER Insurance_RefNo
ON dbo.App_Insurance_Details
INSTEAD OF INSERT
AS
BEGIN
    SET NOCOUNT ON;

    -- temp table banegi inserted rows ki
    SELECT  *,
            dbo.GetAutoGenNumberSimple('Insurance_Ref/') AS NewRef
    INTO #Temp
    FROM Inserted;

    -- Final insert with generated RefNo
    INSERT INTO dbo.App_Insurance_Details
    (
        PolicyNo,
        CustomerName,
        DeptID,
        Location,
        Sum_Insured,
        StartDate,
        ExpiryDate,
        Status,
        CreatedBy,
        CreatedOn,
        Old_RefNo,
        Revised_Sum_Inserted,
        Revised_Sum_Inserted_Attach,
        Revised_StartDate,
        Revised_Expiry_Period,
        Renew_CreatedBy,
        Renew_CreatedOn,
        Insurance_RefNo
    )
    SELECT 
        PolicyNo,
        CustomerName,
        DeptID,
        Location,
        Sum_Insured,
        StartDate,
        ExpiryDate,
        Status,
        CreatedBy,
        CreatedOn,
        Old_RefNo,
        Revised_Sum_Inserted,
        Revised_Sum_Inserted_Attach,
        Revised_StartDate,
        Revised_Expiry_Period,
        Renew_CreatedBy,
        Renew_CreatedOn,
        NewRef
    FROM #Temp;
END







CREATE TRIGGER Insurance_Renewal_RefNo
ON App_Insurance_Details
AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @NewRef VARCHAR(255);
    DECLARE @ID INT;

    -- Sirf un records par kaam karo jaha
    -- RenewalRequired = Yes hua ho
    -- aur OldRef blank ho (means pehli baar renewal)
    DECLARE Cur CURSOR LOCAL FOR
    SELECT I.ID
    FROM Inserted I
    INNER JOIN Deleted D ON I.ID = D.ID
    WHERE 
        I.RenewalRequired = 'Yes'   -- user ne Yes select kiya
        AND D.RenewalRequired <> 'Yes'  -- pehle Yes nahi tha
        AND I.OldRef IS NULL;       -- pehle renewal nahi hua tha

    OPEN Cur;
    FETCH NEXT FROM Cur INTO @ID;

    WHILE @@FETCH_STATUS = 0
    BEGIN
        
        -- STEP-1 : Purana Ref OldRef me daal do
        UPDATE App_Insurance_Details
        SET OldRef = RefNo
        WHERE ID = @ID
          AND OldRef IS NULL;

        -- STEP-2 : NEW Ref Generate karo
        EXEC dbo.GetAutoGenNumberSimple 
             @p1='Insurance_Ref',
             @OutPut=@NewRef OUTPUT;

        -- STEP-3 : RefNo me NEW VALUE daal do
        UPDATE App_Insurance_Details
        SET RefNo = @NewRef
        WHERE ID = @ID;

        FETCH NEXT FROM Cur INTO @ID;
    END

    CLOSE Cur;
    DEALLOCATE Cur;
END






CREATE TRIGGER Insurance_Renewal_RefNo
ON App_Insurance_Details
AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @NewRef VARCHAR(255);
    DECLARE @ID INT;

    -- Sirf un records par kaam karo jaha
    -- RenewalRequired = Yes hua ho
    -- aur OldRef blank ho (means pehli baar renewal)
    DECLARE Cur CURSOR LOCAL FOR
    SELECT I.ID
    FROM Inserted I
    INNER JOIN Deleted D ON I.ID = D.ID
    WHERE 
        I.RenewalRequired = 'Yes'   -- user ne Yes select kiya
        AND D.RenewalRequired <> 'Yes'  -- pehle Yes nahi tha
        AND I.OldRef IS NULL;       -- pehle renewal nahi hua tha

    OPEN Cur;
    FETCH NEXT FROM Cur INTO @ID;

    WHILE @@FETCH_STATUS = 0
    BEGIN
        
        -- STEP-1 : Purana Ref OldRef me daal do
        UPDATE App_Insurance_Details
        SET OldRef = RefNo
        WHERE ID = @ID
          AND OldRef IS NULL;

        -- STEP-2 : NEW Ref Generate karo
        EXEC dbo.GetAutoGenNumberSimple 
             @p1='Insurance_Ref',
             @OutPut=@NewRef OUTPUT;

        -- STEP-3 : RefNo me NEW VALUE daal do
        UPDATE App_Insurance_Details
        SET RefNo = @NewRef




CREATE TRIGGER Insurance_Renewal_RefNo
ON App_Insurance_Details
AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @NewRef VARCHAR(255);
    DECLARE @ID INT;

    DECLARE Cur CURSOR LOCAL FOR
    SELECT I.ID
    FROM Inserted I
    INNER JOIN Deleted D ON I.ID = D.ID
    WHERE 
        D.OldRef IS NULL          -- OLD REF pehle blank tha  
        AND D.RefNo IS NOT NULL   -- RefNo already tha
        AND I.RefNo = D.RefNo;    -- Approval update me Ref change na ho

    OPEN Cur;
    FETCH NEXT FROM Cur INTO @ID;

    WHILE @@FETCH_STATUS = 0
    BEGIN
        
        -- STEP-1 : Purana Ref OldRef me daal do
        UPDATE App_Insurance_Details
        SET OldRef = RefNo
        WHERE ID = @ID
          AND OldRef IS NULL;

        -- STEP-2 : Naya Ref Generate karo
        EXEC dbo.GetAutoGenNumberSimple 
             @p1='Insurance_Ref',
             @OutPut=@NewRef OUTPUT;

        -- STEP-3 : RefNo update karo
        UPDATE App_Insurance_Details
        SET RefNo = @NewRef
        WHERE ID = @ID;

        FETCH NEXT FROM Cur INTO @ID;
    END

    CLOSE Cur;
    DEALLOCATE Cur;

END






CREATE TRIGGER Insurance_Renewal_RefNo
ON App_Insurance_Details
AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @NewRef VARCHAR(255);

    --------------------------------------------------
    -- STEP-1 : Sirf un record par kaam karo jaha 
    -- OldRef NULL hai (means pehla renewal ho raha hai)
    --------------------------------------------------
    UPDATE A
    SET OldRef = A.RefNo
    FROM App_Insurance_Details A
    INNER JOIN Inserted I ON A.ID = I.ID
    WHERE A.OldRef IS NULL     -- pehli baar hi copy karna
      AND A.RefNo IS NOT NULL; -- ensure ref already hai
    

    --------------------------------------------------
    -- STEP-2 : NEW Ref Generate karo
    --------------------------------------------------
    EXEC dbo.GetAutoGenNumberSimple
        @p1 = 'Insurance_Ref',
        @OutPut = @NewRef OUTPUT;


    --------------------------------------------------
    -- STEP-3 : New RefNo update karo SIRF unhi rows me
    -- jaha OldRef already set ho chuka hai
    --------------------------------------------------
    UPDATE A
    SET RefNo = @NewRef
    FROM App_Insurance_Details A
    INNER JOIN Inserted I ON A.ID = I.ID
    WHERE A.OldRef IS NOT NULL;
END
    
    
    
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
                        Rows="4"
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


after entering subjective value in the textbox slide is not working 
