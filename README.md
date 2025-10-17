 public class AppTarget
 {
     public Guid ID { get; set; }
     public Guid KPIID { get; set; }
     public Guid? FinYearID { get; set; }
     public Guid? BasisOfTarget { get; set; }
     public string? BaseLine { get; set; }
     public string? Target { get; set; }
     public string? BenchMarkPatner { get; set; }
     public string? UCL { get; set; }
     public string? LCL { get; set; }
     public string? CreatedBy { get; set; }
     public string? BenchMarkValue { get; set; }
     public Guid? FinYearID1 { get; set; }
     public string? Target1 { get; set; }
     public Guid? FinYearID2 { get; set; }
     public string? Target2 { get; set; }
     public Guid? FinYearID3 { get; set; }
     public string? Target3 { get; set; }
     public Guid? FinYearID4 { get; set; }
     public string? Target4 { get; set; }
     public Guid? PeriodicityID { get; set; }
     public string? Relevant_comparative_available { get; set; }
     public string? Relevant_comparative_available_Value { get; set; }
     public string? Relevant_comparative_available_Details { get; set; }
     public string? Current_performance_better_than_comparative { get; set; }
     public string? Current_performance_better_than_comparative_Value { get; set; }
     public string? Current_performance_better_than_comparative_Details { get; set; }
     public string? Theoretical_limit_known { get; set; }
     public string? Theoretical_limit_known_Value { get; set; }
     public string? Theoretical_limit_known_Details { get; set; }
     public string? Statutory_standard_guidline_known { get; set; }
     public string? Statutory_standard_guidline_known_Value { get; set; }
     public string? Statutory_standard_guidline_known_Details { get; set; }
     public string? Current_performance_better_than_statutory_standard { get; set; }
     public string? Current_performance_better_than_statutory_standard_Value { get; set; }
     public string? Current_performance_better_than_statutory_standard_Details { get; set; }
     public string? Internal_Benchmark_available { get; set; }
     public string? Internal_Benchmark_available_Value { get; set; }
     public string? Internal_Benchmark_available_Details { get; set; }
     public string? Historical_bast_available { get; set; }
     public string? Historical_bast_available_Value { get; set; }
     public string? Historical_bast_available_Details { get; set; }
 
 }

and this is my Table structure 

CREATE TABLE [dbo].[App_TargetSetting_NOPR] (
    [ID]                                                         UNIQUEIDENTIFIER NOT NULL default newid(),
    [KPIID]                                                      UNIQUEIDENTIFIER NOT NULL,
    [FinYearID]                                                  UNIQUEIDENTIFIER NULL,
    [BasisOfTarget]                                              UNIQUEIDENTIFIER NULL,
    [BaseLine]                                                   VARCHAR (250)    NULL,
    [Target]                                                     VARCHAR (250)    NULL,
    [BenchMarkPatner]                                            VARCHAR (100)    NULL,
    [UCL]                                                        VARCHAR (10)     NULL,
    [LCL]                                                        VARCHAR (50)     NULL,
    [CreatedBy]                                                  VARCHAR (50)     NULL,
    [BenchMarkValue]                                             VARCHAR (50)     NULL,
    [FinYearID1]                                                 UNIQUEIDENTIFIER NULL,
    [Target1]                                                    VARCHAR (250)    NULL,
    [FinYearID2]                                                 UNIQUEIDENTIFIER NULL,
    [Target2]                                                    VARCHAR (250)    NULL,
    [FinYearID3]                                                 UNIQUEIDENTIFIER NULL,
    [Target3]                                                    VARCHAR (250)    NULL,
    [FinYearID4]                                                 UNIQUEIDENTIFIER NULL,
    [Target4]                                                    VARCHAR (250)    NULL,
    [PeriodicityID]                                              UNIQUEIDENTIFIER NULL,
    [Relevant_comparative_available]                             VARCHAR (10)     NULL,
    [Relevant_comparative_available_Value]                       VARCHAR (10)     NULL,
    [Relevant_comparative_available_Details]                     VARCHAR (50)     NULL,
    [Current_performance_better_than_comparative]                VARCHAR (10)     NULL,
    [Current_performance_better_than_comparative_Value]          VARCHAR (10)     NULL,
    [Current_performance_better_than_comparative_Details]        VARCHAR (50)     NULL,
    [Theoretical_limit_known]                                    VARCHAR (10)     NULL,
    [Theoretical_limit_known_Value]                              VARCHAR (10)     NULL,
    [Theoretical_limit_known_Details]                            VARCHAR (50)     NULL,
    [Statutory_standard_guidline_known]                          VARCHAR (10)     NULL,
    [Statutory_standard_guidline_known_Value]                    VARCHAR (10)     NULL,
    [Statutory_standard_guidline_known_Details]                  VARCHAR (50)     NULL,
    [Current_performance_better_than_statutory_standard]         VARCHAR (10)     NULL,
    [Current_performance_better_than_statutory_standard_Value]   VARCHAR (10)     NULL,
    [Current_performance_better_than_statutory_standard_Details] VARCHAR (50)     NULL,
    [Internal_Benchmark_available]                               VARCHAR (10)     NULL,
    [Internal_Benchmark_available_Value]                         VARCHAR (10)     NULL,
    [Internal_Benchmark_available_Details]                       VARCHAR (50)     NULL,
    [Historical_bast_available]                                  VARCHAR (10)     NULL,
    [Historical_bast_available_Value]                            VARCHAR (10)     NULL,
    [Historical_bast_available_Details]                          VARCHAR (50)     NULL,
    CONSTRAINT [PK_App_TargetSetting_NOPR] PRIMARY KEY CLUSTERED ([ID] ASC)
);


please make DBContext like this for the table structure
modelBuilder.Entity<AppFormDetail>(entity =>
{
    entity.ToTable("App_FormDetails_NOPR");

    entity.Property(e => e.Id)
        .HasColumnName("ID")
        .HasDefaultValueSql("(newid())");

    entity.Property(e => e.Description)
        .HasMaxLength(100)
        .IsUnicode(false);

    entity.Property(e => e.FormName)
        .HasMaxLength(100)
        .IsUnicode(false);
});
