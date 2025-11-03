SELECT 
    KI.ID AS KPIID,
    KD.ID,
    KD.Value,
    KI.Company,
    TR.FinYearID,
    KI.Division,
    TR.ID AS TSID,
    KI.Department,
    KI.Section,
    pm.PeriodicityName,
    KI.KPIDetails,
    KI.UnitID,
    SF.FinYear,
    KI.CreatedBy,
    KI.KPICode,
    KI.PeriodicityID,
    TR.BaseLine,
    TR.Target,
    TR.BenchMarkPatner,
    TR.BenchMarkValue,
    UM.UnitCode,
    KI.NoofDecimal
FROM App_KPIMaster_NOPR KI
LEFT JOIN App_UOM_NOPR UM ON KI.UnitID = UM.ID
LEFT JOIN App_PeriodicityMaster_NOPR pm ON KI.PeriodicityID = pm.ID
LEFT JOIN (
    SELECT 
        k1.*
    FROM App_KPIDetails_NOPR k1
    INNER JOIN (
        SELECT KPIID, MAX(CreatedOn) AS MaxCreatedOn
        FROM App_KPIDetails_NOPR
        GROUP BY KPIID
    ) k2 ON k1.KPIID = k2.KPIID AND k1.CreatedOn = k2.MaxCreatedOn
) KD ON KD.KPIID = KI.ID
LEFT JOIN App_TargetSetting_NOPR TR ON TR.KPIID = KI.ID
LEFT JOIN App_Sys_FinYear SF ON TR.FinYearID = SF.ID
WHERE
    KI.KPISPOC = @UserId 
    AND KI.Deactivate IS NULL
    AND (@search IS NULL OR KI.KPICode LIKE '%' + @search + '%' OR UM.UnitCode LIKE '%' + @search + '%')
    AND (@search2 IS NULL OR KI.Department LIKE '%' + @search2 + '%')
    AND (@search3 IS NULL OR KI.KPIDetails LIKE '%' + @search3 + '%')
ORDER BY KI.KPIDetails
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;




main query
SELECT 
    KI.ID as KPIID,KD.ID,KD.Value, KI.Company,TR.FinYearID , KI.Division,TR.ID as TSID, KI.Department, KI.Section, KI.ID as KPIID, 
    pm.PeriodicityName, KI.KPIDetails, KI.UnitID, SF.FinYear, KI.CreatedBy, 
    KI.KPICode, KI.PeriodicityID, TR.BaseLine, TR.Target, TR.BenchMarkPatner, 
    TR.BenchMarkValue, UM.UnitCode, KI.NoofDecimal
FROM App_KPIMaster_NOPR KI 
LEFT JOIN App_UOM_NOPR UM ON KI.UnitID = UM.ID 
LEFT JOIN App_PeriodicityMaster_NOPR pm ON KI.PeriodicityID = pm.ID 
LEFT JOIN App_KPIDetails_NOPR KD ON KD.KPIID = KI.ID 
LEFT JOIN App_TargetSetting_NOPR TR ON TR.KPIID = KI.ID 
LEFT JOIN App_Sys_FinYear SF ON TR.FinYearID = SF.ID
WHERE
KI.KPISPOC =@UserId AND KI.Deactivate is null AND
    (@search IS NULL OR KI.KPICode LIKE '%' + @search + '%' OR UM.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR KI.Department LIKE '%' + @search2 + '%')
AND (@search3 IS NULL OR KI.KPIDetails LIKE '%' + @search3 + '%')
ORDER BY KI.KPIDetails
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY

sub table which stores multiple rows for each PeriodTransactionID but for same KPI 

  public class AppKPIDetails
  {
      [Key]
      public Guid ID { get; set; }
      public Guid KPIID { get; set; }
      public Guid? PeriodTransactionID { get; set; }
      public decimal? Value { get; set; }
      public Guid? FinYearID { get; set; }
      public string? CreatedBy { get; set; }
      public DateTime? CreatedOn { get; set; }
      public DateTime? KPIDate { get; set; }
      public decimal? YTDValue { get; set; }
      public int? KPITime { get; set; }

  }

from the main query i am getting multiples record but for each kpi show only one 
