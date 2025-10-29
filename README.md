  CREATE TABLE [dbo].[App_KPIDetails_NOPR] (
    [ID]        UNIQUEIDENTIFIER DEFAULT (newid()) NOT NULL,
    [KPIID]     UNIQUEIDENTIFIER NOT NULL,
    [PeriodID]  UNIQUEIDENTIFIER NULL,
    [Value]     NUMERIC (18, 4)  NULL,
    [FinYearID] UNIQUEIDENTIFIER NULL,
    [CreatedBy] VARCHAR (50)     NULL,
    [CreatedOn] DATETIME         NULL,
    [KPIDate]   DATETIME         NULL,
    [YTDValue]  NUMERIC (18, 4)  NULL,
    [KPITime]   INT              NULL,
    CONSTRAINT [PK_App_KPIDetails_NOPR] PRIMARY KEY CLUSTERED ([ID] ASC)
);
