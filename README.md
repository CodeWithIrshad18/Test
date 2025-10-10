this is my table structure 
 
    [ID]                 UNIQUEIDENTIFIER NOT NULL,
    [CompanyID]          UNIQUEIDENTIFIER NULL,
    [DivisionID]         UNIQUEIDENTIFIER NULL,
    [DepartmentID]       UNIQUEIDENTIFIER NULL,
    [SectionID]          UNIQUEIDENTIFIER NULL,
    [KPICode]            VARCHAR (50)     NOT NULL,
    [KPIDetails]         VARCHAR (MAX)    NOT NULL,
    [UnitID]             UNIQUEIDENTIFIER NOT NULL,
    [CreatedBy]          VARCHAR (50)     NULL,
    [PeriodicityID]      UNIQUEIDENTIFIER NULL,
    [GoodPerformance]    NVARCHAR (100)   NULL,
    [HistoricalBest]     NUMERIC (18, 2)  NULL,
    [HistoricalBestYear] UNIQUEIDENTIFIER NULL,
    [TheoreticalBest]    NUMERIC (18, 2)  NULL,
    [NoofDecimal]        INT              NULL,
    [KPIDefination]      VARCHAR (MAX)    NULL,
    [PerspectiveID]      UNIQUEIDENTIFIER NULL,
    [TypeofKPIID]        UNIQUEIDENTIFIER NULL,
    [LTPSTPID]           UNIQUEIDENTIFIER NULL,
    [Central_Local]      NVARCHAR (50)    NULL,
    [KPIUpto]            VARCHAR (50)     NULL,
    [Deactivate]         BIT              NULL,
    [DeactivateOn]       DATETIME         NULL,
    [KPILevel]           VARCHAR (50)     NULL,
    [KPIMode]            VARCHAR (50)     NULL,
    [SourceData]         VARCHAR (500)    NULL,
    [COMode]             VARCHAR (500)    NULL,
    [PCNCode]            VARCHAR (500)    NULL,
    CONSTRAINT [PK_App_KPIMaster_NOPR] PRIMARY KEY CLUSTERED ([ID] ASC),
    CONSTRAINT [FK_App_CompanyMaster_App_KPIMaster] FOREIGN KEY ([ID]) REFERENCES [dbo].[App_KPIMaster] ([ID]),
    CONSTRAINT [FK_App_KPIMaster_App_KPIMaster] FOREIGN KEY ([ID]) REFERENCES [dbo].[App_KPIMaster] ([ID]),
    CONSTRAINT [FK_App_KPIMaster_App_KPIMaster1] FOREIGN KEY ([ID]) REFERENCES [dbo].[App_KPIMaster] ([ID]),
    CONSTRAINT [FK_App_KPIMaster_App_UOM] FOREIGN KEY ([UnitID]) REFERENCES [dbo].[App_UOM] ([ID])
);


 please give model and model context like this 

 modelBuilder.Entity<AppBasisOfTarget>(entity =>
 {
     entity.ToTable("App_BasisOfTarget_NOPR");

     entity.Property(e => e.ID)
         .HasColumnName("ID")
         .HasDefaultValueSql("(newid())");

     entity.Property(e => e.TargetCode)
         .HasMaxLength(50)
         .IsUnicode(false);

     entity.Property(e => e.TargetDescription)
         .HasMaxLength(100)
         .IsUnicode(false);

     entity.Property(e => e.CreatedBy)
         .HasMaxLength(50)
         .IsUnicode(false);
 });
