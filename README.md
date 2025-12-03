DECLARE @UserId NVARCHAR(50) = '151514';

SELECT DISTINCT
    k.ID,
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
    ts.Comparative,
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

-- Join Permission Table on user ID (NOT KPISPOC)
LEFT JOIN App_PermissionMaster pm ON pm.Pno = @UserId

WHERE
    (
        pm.Type = 'Admin'               -- Admin sees EVERYTHING
        OR
        k.KPISPOC = @UserId             -- Non-admin sees only their records
    )
    AND (k.Deactivate IS NULL OR k.Deactivate = 0)

ORDER BY k.KPIDetails;




SELECT DISTINCT
    k.ID,
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
    ts.Comparative,
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
LEFT JOIN App_PermissionMaster pm ON k.KPISPOC = pm.Pno

WHERE
    (pm.Type = 'Admin' OR k.KPISPOC = pm.Pno)
    AND (k.Deactivate IS NULL OR k.Deactivate = 0)

ORDER BY k.KPIDetails;

 
 
 SELECT DISTINCT
    k.ID,
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
    ts.Comparative,
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
LEFT JOIN App_PermissionMaster pm ON k.KPISPOC = pm.Pno

WHERE
k.KPISPOC ='151514' and (k.Deactivate IS NULL OR k.Deactivate = 0) 
ORDER BY k.KPIDetails


select * from App_PermissionMaster

ID	Pno	Type
C77F104E-9528-4775-9D79-CB961F50605F	151514	Admin
