CREATE TABLE [dbo].[App_PeriodicityMaster_NOPR] (
    [ID]               UNIQUEIDENTIFIER DEFAULT (newid()) NOT NULL,
    [PeriodicityCode]  VARCHAR (50)     NOT NULL,
    [PeriodicityName]  VARCHAR (50)     NOT NULL,
    [CreatedBy]        VARCHAR (50)     NULL,
    [MaximumEntryDate] VARCHAR (50)     NULL,
    CONSTRAINT [PK_App_PeriodicityMaster_NOPR] PRIMARY KEY CLUSTERED ([ID] ASC)
);
