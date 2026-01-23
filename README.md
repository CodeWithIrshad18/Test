SELECT  
    km.KPICode,
    km.KPIDetails AS Areas,
    km.Division,
    km.Department,
    u.UnitCode AS UoM,

    tsd.TargetValue AS JanuaryTarget,
    kd.Value AS JanuaryActual

FROM App_KPIMaster_NOPR km

LEFT JOIN App_TargetSetting_NOPR ts
    ON ts.KPIID = km.ID

LEFT JOIN App_TargetSettingDetails_NOPR tsd
    ON tsd.MasterID = ts.ID
   AND tsd.PeriodicityTransactionID = 'JAN'   -- 🔥 month filter here

LEFT JOIN App_PeriodicityTransaction_NOPR pt
    ON pt.PeriodicityName = tsd.PeriodicityTransactionID
   AND pt.PeriodicityID = ts.PeriodicityID   -- 🔥 same as old query

LEFT JOIN App_KPIDetails_NOPR kd
    ON kd.KPIID = km.ID
   AND kd.PeriodTransactionID = pt.ID        -- 🔥 EXACT SAME actual mapping

LEFT JOIN App_UOM_NOPR u
    ON km.UnitID = u.ID

WHERE km.Department = 'Human Resource & Industrial Relations'

ORDER BY km.KPICode;





SELECT  
    km.KPICode,
    km.KPIDetails,
    km.Division,
    km.Department,
    km.Section,
    t.TypeofKPI,
    u.UnitCode,
    p.PeriodicityName,
    pt.PeriodicityName AS Month,
    tsd.TargetValue,
    kd.Value AS ActualValue,
    kd.YTDValue
FROM App_KPIMaster_NOPR km
LEFT JOIN App_TargetSetting_NOPR ts ON ts.KPIID = km.ID
LEFT JOIN App_UOM_NOPR u ON km.UnitID = u.ID
LEFT JOIN App_PeriodicityMaster_NOPR p ON km.PeriodicityID = p.ID
LEFT JOIN App_TargetSettingDetails_NOPR tsd ON tsd.MasterID = ts.ID
LEFT JOIN App_TypeofKPI_NOPR t ON km.TypeofKPIID = t.ID
LEFT JOIN App_PeriodicityTransaction_NOPR pt 
       ON pt.PeriodicityName = tsd.PeriodicityTransactionID
      AND pt.PeriodicityID = ts.PeriodicityID
LEFT JOIN App_KPIDetails_NOPR kd
       ON kd.KPIID = km.ID
      AND kd.PeriodTransactionID = pt.ID
WHERE (@KpiId IS NULL OR km.ID = @KpiId)
ORDER BY km.KPICode,pt.Sl_no;




SELECT  
    km.KPICode,
    km.KPIDetails AS Areas,
    km.Division,
    km.Department,
    u.UnitCode AS UoM,

    tsd.TargetValue AS JanuaryTarget,
    kd.Value AS JanuaryActual

FROM App_KPIMaster_NOPR km

INNER JOIN App_TargetSetting_NOPR ts
    ON ts.KPIID = km.ID

INNER JOIN App_TargetSettingDetails_NOPR tsd
    ON tsd.MasterID = ts.ID
   AND tsd.PeriodicityTransactionID = 'JAN'   

INNER JOIN App_PeriodicityTransaction_NOPR pt
    ON pt.PeriodicityName = tsd.PeriodicityTransactionID
   AND pt.PeriodicityID = ts.PeriodicityID   

LEFT JOIN App_KPIDetails_NOPR kd
    ON kd.KPIID = km.ID
   AND kd.PeriodTransactionID = pt.ID        

LEFT JOIN App_UOM_NOPR u
    ON km.UnitID = u.ID

WHERE km.Department = 'Human Resource & Industrial Relations'

ORDER BY km.KPICode;
