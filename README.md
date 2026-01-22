SELECT
    p.TRBDGDA_BD_PNO as PNo,e.ema_dept_desc,

    SUM(CASE 
            WHEN p.TRBDGDA_BD_DATE >= '2025-11-01'
             AND p.TRBDGDA_BD_DATE <  '2025-12-01'
            THEN 1 ELSE 0 
        END) AS November_Punches,

    SUM(CASE 
            WHEN p.TRBDGDA_BD_DATE >= '2025-12-01'
             AND p.TRBDGDA_BD_DATE <  '2026-01-01'
            THEN 1 ELSE 0 
        END) AS December_Punches,

    SUM(CASE 
            WHEN p.TRBDGDA_BD_DATE >= '2026-01-01'
             AND p.TRBDGDA_BD_DATE <  '2026-02-01'
            THEN 1 ELSE 0 
        END) AS January_Punches

FROM T_TRBDGDAT_EARS p
INNER JOIN SAPHRDB.dbo.T_EMPL_ALL e
    ON e.ema_perno = p.TRBDGDA_BD_PNO
WHERE p.TRBDGDA_BD_ENTRYUID = 'MOBILE'
  AND e.ema_pyrl_area IN ('JS', 'ZZ')
  AND p.TRBDGDA_BD_DATE >= '2025-11-01'
  AND p.TRBDGDA_BD_DATE <  '2026-02-01'

GROUP BY p.TRBDGDA_BD_PNO,e.ema_dept_desc
ORDER BY e.ema_dept_desc;
