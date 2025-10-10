public partial class AppKpiMaster
{
    public Guid ID { get; set; }
    public Guid? CompanyID { get; set; }
    public Guid? DivisionID { get; set; }
    public Guid? DepartmentID { get; set; }
    public Guid? SectionID { get; set; }
    public string KPICode { get; set; }
    public string KPIDetails { get; set; }
    public Guid UnitID { get; set; }
    public string CreatedBy { get; set; }
    public Guid? PeriodicityID { get; set; }
    public string GoodPerformance { get; set; }
    public decimal? HistoricalBest { get; set; }
    public Guid? HistoricalBestYear { get; set; }
    public decimal? TheoreticalBest { get; set; }
    public int? NoofDecimal { get; set; }
    public string KPIDefination { get; set; }
    public Guid? PerspectiveID { get; set; }
    public Guid? TypeofKPIID { get; set; }
    public Guid? LTPSTPID { get; set; }
    public string Central_Local { get; set; }
    public string KPIUpto { get; set; }
    public bool? Deactivate { get; set; }
    public DateTime? DeactivateOn { get; set; }
    public string KPILevel { get; set; }
    public string KPIMode { get; set; }
    public string SourceData { get; set; }
    public string COMode { get; set; }
    public string PCNCode { get; set; }

    // Navigation properties (if needed)
    public virtual AppUOM Unit { get; set; }
}

modelBuilder.Entity<AppKpiMaster>(entity =>
{
    entity.ToTable("App_KPIMaster_NOPR");

    entity.HasKey(e => e.ID)
        .HasName("PK_App_KPIMaster_NOPR");

    entity.Property(e => e.ID)
        .HasColumnName("ID")
        .HasDefaultValueSql("(newid())");

    entity.Property(e => e.KPICode)
        .HasMaxLength(50)
        .IsUnicode(false);

    entity.Property(e => e.KPIDetails)
        .IsUnicode(false);

    entity.Property(e => e.CreatedBy)
        .HasMaxLength(50)
        .IsUnicode(false);

    entity.Property(e => e.GoodPerformance)
        .HasMaxLength(100);

    entity.Property(e => e.HistoricalBest)
        .HasColumnType("numeric(18, 2)");

    entity.Property(e => e.TheoreticalBest)
        .HasColumnType("numeric(18, 2)");

    entity.Property(e => e.KPIDefination)
        .IsUnicode(false);

    entity.Property(e => e.Central_Local)
        .HasMaxLength(50);

    entity.Property(e => e.KPIUpto)
        .HasMaxLength(50)
        .IsUnicode(false);

    entity.Property(e => e.KPILevel)
        .HasMaxLength(50)
        .IsUnicode(false);

    entity.Property(e => e.KPIMode)
        .HasMaxLength(50)
        .IsUnicode(false);

    entity.Property(e => e.SourceData)
        .HasMaxLength(500)
        .IsUnicode(false);

    entity.Property(e => e.COMode)
        .HasMaxLength(500)
        .IsUnicode(false);

    entity.Property(e => e.PCNCode)
        .HasMaxLength(500)
        .IsUnicode(false);

    entity.Property(e => e.DeactivateOn)
        .HasColumnType("datetime");

    // Foreign Key: UnitID → App_UOM
    entity.HasOne(d => d.Unit)
        .WithMany()
        .HasForeignKey(d => d.UnitID)
        .HasConstraintName("FK_App_KPIMaster_App_UOM");
});





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
