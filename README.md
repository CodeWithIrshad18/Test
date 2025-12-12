CREATE PROCEDURE [dbo].[GetAutoGenNumberSimple]
(
    @p1 VARCHAR(50),             -- ObjName
    @OutPut VARCHAR(255) OUTPUT
)
AS
BEGIN
    DECLARE 
        @prefix VARCHAR(20),
        @postfix VARCHAR(20),
        @num INT,
        @result VARCHAR(255);

    -- Fetch auto number details
    SELECT 
        @prefix = Prefix,
        @postfix = Postfix,
        @num = number
    FROM App_Sys_AutoNumber
    WHERE ObjName = @p1 AND IsActive = 1;

    -- Build final output: IN/26/4554
    SET @result = @prefix + '/' + @postfix + '/' + CAST(@num AS VARCHAR(20));

    -- Increment for next use
    UPDATE App_Sys_AutoNumber
    SET number = number + 1
    WHERE ObjName = @p1 AND IsActive = 1;

    SET @OutPut = @result;
END



CREATE TRIGGER [Insurance_RefNo]
	ON [dbo].[App_Insurance_Details]
   INSTEAD OF INsert
AS 
BEGIN
	-- SET NOCOUNT ON added to prevent extra result sets from
	-- interfering with SELECT statements.
	SET NOCOUNT ON;
SELECT * INTO #Inserted FROM Inserted

declare		@OutPut varchar(255)
declare		@p1 varchar(255)

EXEC	[dbo].[GetAutoGenNumberNoLeadingZero]
		@p1='Insurance_Ref/',
		@OutPut = @OutPut OUTPUT

     UPDATE #Inserted SET RefNo = @OutPut
     INSERT INTO [App_Insurance_Details] SELECT * FROM #Inserted
    -- Insert statements for trigger here
END


CREATE PROCEDURE [dbo].[GetAutoGenNumberNoLeadingZero]
	(
	-- Add the parameters for the function here
	@p1 varchar(50),
	@OutPut varchar(255) output
)
AS
DECLARE @FY AS VARCHAR(5)
BEGIN
	declare @MaxId as varchar(255)
	
	SELECT @FY = (CASE WHEN (MONTH(GETDATE())) <= 3 
				 THEN 
					convert(varchar(4), (YEAR(GETDATE())-1)%100) + '-' + convert(varchar(4), YEAR(GETDATE())%100)
				 ELSE
					convert(varchar(4),YEAR(GETDATE())%100)+ '-' + convert(varchar(4),(YEAR(GETDATE())%100)+1)END);

	select @MaxId= cast(number as varchar(20)) + '/' + RIGHT('00'+ CAST(DATEPART(mm, GETDATE()) AS varchar(2)), 2) +'/' + @FY from dbo.App_Sys_AutoNumber
 where objName=@p1 and isactive=1
	update App_Sys_AutoNumber set number=number+1 where objName=@p1 and isactive=1
set @OutPut= @MaxId
END


IN/26/4554
