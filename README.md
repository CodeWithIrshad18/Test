SELECT DISTINCT
    k.ID,
    k.Deactivate,
    k.DeactivateFrom,
    k.DeactivateTo,
    ts.*,
    k.KPIDetails,
    u.UnitCode,
    k.UnitID,
    p.PeriodicityName,
    k.KPICode,
    k.Department,
    k.Division,
    k.Section,
    k.PeriodicityID,
    k.UnitID,

    CASE 
        WHEN EXISTS (
            SELECT 1 
            FROM App_TargetSettingDetails_NOPR td 
            WHERE td.MasterId = ts.ID
        ) THEN 'Y'
        ELSE 'N'
    END AS TargetSetStatus

FROM App_KPIMaster_NOPR k
LEFT JOIN App_PeriodicityMaster_NOPR p ON k.PeriodicityID = p.ID
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
LEFT JOIN App_Prespectives_NOPR ps ON k.PerspectiveID = ps.ID
LEFT JOIN App_TypeofKPI_NOPR t ON k.TypeofKPIID = t.ID
LEFT JOIN App_GoodPerformance_NOPR g ON k.GoodPerformance = g.ID
LEFT JOIN App_TargetSetting_NOPR ts ON k.ID = ts.KPIID
WHERE
k.KPISPOC ='151514' and k.Deactivate is null and k.DeactivateFrom=
