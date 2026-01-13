select * from App_PeriodicityTransaction_NOPR where PeriodicityID ='CDD263FE-5946-4BD2-A2C7-B7E31C19640A'


SELECT  
    km.ID AS KPIID,
    km.KPICode,
    km.KPIDetails,
    km.Company,
    km.Division,
    km.Department,
    km.Section,

    ts.ID AS TargetMasterID,
    ts.FinYearID,
    ts.PeriodicityID,

    tsd.PeriodicityTransactionID,
    tsd.TargetValue,

    kd.Value AS ActualValue,
    kd.YTDValue,
    kd.KPIDate

FROM App_KPIMaster_NOPR km

LEFT JOIN App_TargetSetting_NOPR ts
    ON ts.KPIID = km.ID

LEFT JOIN App_TargetSettingDetails_NOPR tsd
    ON tsd.MasterID = ts.ID

LEFT JOIN App_KPIDetails_NOPR kd
    ON kd.KPIID = km.ID

WHERE km.ID = 'AFC775C0-6B01-4270-BFB6-B9264ACC6334'
ORDER BY tsd.PeriodicityTransactionID;
