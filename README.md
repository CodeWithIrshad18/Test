CREATE TRIGGER [dbo].[KPI_ON_Insert_NOPR] 
ON dbo.App_KPIMaster_NOPR 
INSTEAD OF INSERT
AS
BEGIN
    SET NOCOUNT ON;

    -- Copy inserted rows into a temp table
    SELECT * INTO #Inserted FROM Inserted;

    DECLARE 
        @Division NVARCHAR(255),
        @Department NVARCHAR(255),
        @Section NVARCHAR(255),
        @Prefix NVARCHAR(50),
        @Output VARCHAR(255);

    -- Assuming one row at a time; if multi-row insert, this logic needs a loop or set-based handling
    SELECT 
        @Division = Division,
        @Department = Department,
        @Section = Section
    FROM #Inserted;

    -- ✅ Determine prefix based on which level has data
    IF (ISNULL(@Section, '') <> '')
        SET @Prefix = 'SE';
    ELSE IF (ISNULL(@Department, '') <> '')
        SET @Prefix = 'DP';
    ELSE IF (ISNULL(@Division, '') <> '')
        SET @Prefix = 'DV';
    ELSE
        SET @Prefix = 'CO';  -- Default prefix if nothing else is found

    -- ✅ Generate KPI Code
    EXEC [dbo].[GetAutoGenNumber]
        @p1 = @Prefix,
        @OutPut = @Output OUTPUT;

    -- ✅ Update KPI Code in the temporary table
    UPDATE #Inserted
    SET KPICode = @Output;

    -- ✅ Insert back into actual table
    INSERT INTO App_KPIMaster_NOPR
    SELECT * FROM #Inserted;
END;
GO



CREATE TRIGGER [dbo].[KPI_ON_Insert_NOPR] 
   ON  dbo.App_KPIMaster_NOPR 
   INSTEAD OF INsert
AS 
BEGIN

	SET NOCOUNT ON;
SELECT * INTO #Inserted FROM Inserted
declare		@Company uniqueidentifier
declare		@Division uniqueidentifier
declare		@Depertment uniqueidentifier
declare		@Section varchar(255)
declare		@prefix nvarchar(50)
declare		@OutPut varchar(255)

select @Company=Company,@Division=Division,@Depertment=Department,@Section=Section from #Inserted

IF (@Section <> '00000000-0000-0000-0000-000000000000')
            SELECT @prefix = 'SE'
else
	IF (@Depertment <> '00000000-0000-0000-0000-000000000000')
            SELECT @prefix = 'DP'
	else
		IF (@Division <> '00000000-0000-0000-0000-000000000000')
            SELECT @prefix = 'DV'
		else
			IF (@Company = '00000000-0000-0000-0000-000000000000' )
				set @prefix = 'CO'
			else
				set @prefix = 'CO'
EXEC	[dbo].[GetAutoGenNumber]
		@p1=@prefix,
		@OutPut = @OutPut OUTPUT

     UPDATE #Inserted SET KPICode = @OutPut
     INSERT INTO App_KPIMaster_NOPR SELECT * FROM #Inserted
END
