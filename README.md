using System;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace YourNamespace.Models
{
    [Table("App_KPIDetails_NOPR")]
    public class AppKPIDetailsNOPR
    {
        [Key]
        public Guid ID { get; set; }

        [Required]
        public Guid KPIID { get; set; }

        public Guid? PeriodID { get; set; }

        public decimal? Value { get; set; }

        public Guid? FinYearID { get; set; }

        [StringLength(50)]
        public string? CreatedBy { get; set; }

        public DateTime? CreatedOn { get; set; }

        public DateTime? KPIDate { get; set; }

        public decimal? YTDValue { get; set; }

        public int? KPITime { get; set; }
    }
}

using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;
using YourNamespace.Models;

namespace YourNamespace.Configurations
{
    public class AppKPIDetailsNOPRConfiguration : IEntityTypeConfiguration<AppKPIDetailsNOPR>
    {
        public void Configure(EntityTypeBuilder<AppKPIDetailsNOPR> entity)
        {
            entity.ToTable("App_KPIDetails_NOPR");

            entity.HasKey(e => e.ID);

            entity.Property(e => e.ID)
                .HasDefaultValueSql("NEWID()");

            entity.Property(e => e.KPIID)
                .IsRequired();

            entity.Property(e => e.Value)
                .HasColumnType("numeric(18,4)");

            entity.Property(e => e.YTDValue)
                .HasColumnType("numeric(18,4)");

            entity.Property(e => e.CreatedBy)
                .HasMaxLength(50)
                .IsUnicode(false);

            entity.Property(e => e.CreatedOn)
                .HasColumnType("datetime");

            entity.Property(e => e.KPIDate)
                .HasColumnType("datetime");

            entity.Property(e => e.KPITime)
                .HasColumnType("int");
        }
    }
}
  
  
  
  
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
