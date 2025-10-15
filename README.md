
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
