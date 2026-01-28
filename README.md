DECLARE @cols AS NVARCHAR(MAX),
        @query AS NVARCHAR(MAX),
        @DeptName AS NVARCHAR(100) = 'Human Resource & Industrial Relations'; 

SELECT @cols = STUFF((
    SELECT ',' + QUOTENAME(PeriodicityName + ' Target') 
               + ',' + QUOTENAME(PeriodicityName + ' Actual')
               + ',' + QUOTENAME(PeriodicityName + ' Perf %')
               + ',' + QUOTENAME(PeriodicityName + ' Act Weight')
    FROM App_PeriodicityTransaction_NOPR    where PeriodicityID='CDD263FE-5946-4BD2-A2C7-B7E31C19640A'
    GROUP BY PeriodicityName, Sl_no
    ORDER BY Sl_no
    FOR XML PATH(''), TYPE).value('.', 'NVARCHAR(MAX)'), 1, 1, '');

SET @query = '
DECLARE @TotalKPIs FLOAT;
SELECT @TotalKPIs = COUNT(*) FROM App_KPIMaster_NOPR WHERE Department = @DeptFilter;

-- Base Weightage (e.g., 4.76% if 21 KPIs)
DECLARE @BaseWeight FLOAT = 100.0 / NULLIF(@TotalKPIs, 0);

SELECT 
    KPICode, KPIDetails, Division, Department, Section, TypeofKPI, UnitCode, GoodPerformance,
    CAST(@BaseWeight AS DECIMAL(18,2)) AS [Weightage (%)],
    ' + @cols + '
FROM 
(
    SELECT  
        km.KPICode, km.KPIDetails, km.Division, km.Department, km.Section,
        t.TypeofKPI, u.UnitCode, gn.Name AS GoodPerformance,
        pt.PeriodicityName + '' '' + val.ValueType AS ColumnHeader,
        CAST(val.Value AS DECIMAL(18,2)) AS Value
    FROM App_KPIMaster_NOPR km
    LEFT JOIN App_UOM_NOPR u ON km.UnitID = u.ID
    LEFT JOIN App_TypeofKPI_NOPR t ON km.TypeofKPIID = t.ID
    LEFT JOIN App_GoodPerformance_NOPR gn ON gn.ID = km.GoodPerformance
    LEFT JOIN App_TargetSetting_NOPR ts ON ts.KPIID = km.ID
    LEFT JOIN App_TargetSettingDetails_NOPR tsd ON tsd.MasterID = ts.ID
    LEFT JOIN App_PeriodicityTransaction_NOPR pt 
           ON pt.PeriodicityName = tsd.PeriodicityTransactionID
          AND pt.PeriodicityID = ts.PeriodicityID
    LEFT JOIN App_KPIDetails_NOPR kd 
           ON kd.KPIID = km.ID 
          AND kd.PeriodTransactionID = pt.ID
    CROSS APPLY (
        SELECT 
            CAST(ISNULL(tsd.TargetValue,0) AS FLOAT) as Tgt, 
            CAST(ISNULL(kd.Value,0) AS FLOAT) as Act,
            CASE 
                WHEN gn.Name LIKE ''%Higher than Target%'' THEN (CAST(ISNULL(kd.Value,0) AS FLOAT) / NULLIF(CAST(tsd.TargetValue AS FLOAT), 0)) * 100
                WHEN gn.Name LIKE ''%Lower than Target%'' THEN (CAST(ISNULL(tsd.TargetValue,0) AS FLOAT) / NULLIF(CAST(kd.Value AS FLOAT), 0)) * 100
                ELSE 0 
            END AS PerfPct
    ) calc
    CROSS APPLY (
        SELECT ''Target'' AS ValueType, calc.Tgt AS Value
        UNION ALL
        SELECT ''Actual'' AS ValueType, calc.Act AS Value
        UNION ALL
        SELECT ''Perf %'' AS ValueType, calc.PerfPct AS Value
        UNION ALL
        -- REVISED INTERNAL LOGIC 4
        SELECT ''Act Weight'' AS ValueType,
            CASE 
                -- If Perf % <= 100%: Simple linear weightage
                WHEN calc.PerfPct <= 100 THEN (calc.PerfPct / 100.0) * @BaseWeight
                
                -- If Perf % > 100%: [100% + (25% * (Perf% - 100%))] * Weightage%
                -- Math: ((100 + (0.25 * (Perf - 100))) / 100) * BaseWeight
                WHEN calc.PerfPct > 100 THEN (100.0 + (0.25 * (calc.PerfPct - 100.0))) * (@BaseWeight / 100.0)
                
                ELSE 0 
            END AS Value
    ) val
    WHERE km.Department = @DeptFilter
) x
PIVOT 
(
    MAX(Value) 
    FOR ColumnHeader IN (' + @cols + ')
) p
ORDER BY KPICode;';

EXEC sp_executesql @query, N'@DeptFilter NVARCHAR(100)', @DeptFilter = @DeptName


Data : 

SE/001	Overall Plant Efficiency -RPH	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Higher than Target	4.76	97.00	96.00	98.97	4.71	97.00	96.00	98.97	4.71	97.00	96.00	98.97	4.71	98.00	95.51	97.46	4.64	98.00	95.58	97.53	4.64	98.00	93.25	95.15	4.53	98.00	94.80	96.73	4.61	98.00	95.21	97.15	4.63	98.00	95.16	97.10	4.62	98.00	0.00	0.00	0.00	98.00	0.00	0.00	0.00	98.00	0.00	0.00	0.00
SE/002	Overall plant efficiency - Water Works	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Higher than Target	4.76	88.00	79.00	89.77	4.27	88.00	78.00	88.64	4.22	88.00	84.00	95.45	4.55	84.00	78.00	92.86	4.42	84.00	82.00	97.62	4.65	84.00	83.80	99.76	4.75	84.00	84.00	100.00	4.76	84.00	83.00	98.81	4.71	84.00	83.40	99.29	4.73	84.00	0.00	0.00	0.00	84.00	0.00	0.00	0.00	84.00	0.00	0.00	0.00
SE/003	Overall plant efficiency - Water Distribution	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Higher than Target	4.76	82.00	79.31	96.72	4.61	82.00	79.40	96.83	4.61	82.00	79.30	96.71	4.61	84.00	78.54	93.50	4.45	84.00	80.59	95.94	4.57	84.00	79.59	94.75	4.51	84.00	79.94	95.17	4.53	84.00	81.30	96.79	4.61	84.00	78.62	93.60	4.46	84.00	0.00	0.00	0.00	84.00	0.00	0.00	0.00	84.00	0.00	0.00	0.00
SE/004	Overall Plant Efficiency - Waste Water	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Higher than Target	4.76	95.00	75.00	78.95	3.76	95.00	82.00	86.32	4.11	95.00	84.00	88.42	4.21	94.00	104.90	111.60	4.90	94.00	99.86	106.23	4.84	94.00	93.53	99.50	4.74	94.00	84.00	89.36	4.26	94.00	78.00	82.98	3.95	94.00	77.00	81.91	3.90	94.00	0.00	0.00	0.00	94.00	0.00	0.00	0.00	94.00	0.00	0.00	0.00
SE/006	Compliance to Waste Segregation at Source	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Higher than Target	4.76	70.00	30.19	43.13	2.05	70.00	30.19	43.13	2.05	70.00	30.19	43.13	2.05	70.00	49.00	70.00	3.33	70.00	49.00	70.00	3.33	70.00	49.00	70.00	3.33	70.00	53.29	76.13	3.63	70.00	53.29	76.13	3.63	70.00	53.29	76.13	3.63	0.00	0.00	NULL	0.00	0.00	0.00	NULL	0.00	0.00	0.00	NULL	0.00
SE/007	Compliance to PM - PSD (Overall upto 415 V Feeder Pillar including Earth Pit Main)	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	Nos.	Higher than Target	4.76	148.00	134.00	90.54	4.31	280.00	312.00	111.43	4.90	309.00	411.00	133.01	5.15	420.00	457.00	108.81	4.87	408.00	349.00	85.54	4.07	470.00	441.00	93.83	4.47	0.00	0.00	NULL	0.00	0.00	0.00	NULL	0.00	0.00	0.00	NULL	0.00	0.00	0.00	NULL	0.00	0.00	0.00	NULL	0.00	0.00	0.00	NULL	0.00
SE/008	Number of HT & LT Breakdown	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	Nos.	Lower than Target	4.76	12.00	20.00	60.00	2.86	11.00	17.00	64.71	3.08	12.00	31.00	38.71	1.84	12.00	19.00	63.16	3.01	11.00	18.00	61.11	2.91	10.00	10.00	100.00	4.76	0.00	0.00	NULL	0.00	0.00	0.00	NULL	0.00	0.00	0.00	NULL	0.00	0.00	0.00	NULL	0.00	0.00	0.00	NULL	0.00	0.00	0.00	NULL	0.00
SE/009	TMH Electrical Supply & Maintenance - Customer Complaint within SLG	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Higher than Target	4.76	95.00	95.00	100.00	4.76	95.00	97.00	102.11	4.79	95.00	92.00	96.84	4.61	99.00	98.73	99.73	4.75	95.00	98.60	103.79	4.81	95.00	98.60	103.79	4.81	0.00	0.00	NULL	0.00	0.00	0.00	NULL	0.00	0.00	0.00	NULL	0.00	0.00	0.00	NULL	0.00	0.00	0.00	NULL	0.00	0.00	0.00	NULL	0.00
SE/010	DDJ Compliance within SLG	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Higher than Target	4.76	97.00	96.00	98.97	4.71	97.00	97.00	100.00	4.76	97.00	91.00	93.81	4.47	90.00	88.00	97.78	4.66	90.00	97.00	107.78	4.85	90.00	97.00	107.78	4.85	90.00	95.00	105.56	4.83	90.00	97.00	107.78	4.85	90.00	90.00	100.00	4.76	90.00	0.00	0.00	0.00	90.00	0.00	0.00	0.00	90.00	0.00	0.00	0.00
SE/011	NDJ Compliance within SLG	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Higher than Target	4.76	96.00	95.00	98.96	4.71	96.00	98.00	102.08	4.79	96.00	99.00	103.13	4.80	90.00	87.00	96.67	4.60	90.00	93.00	103.33	4.80	90.00	88.00	97.78	4.66	90.00	89.00	98.89	4.71	90.00	91.00	101.11	4.78	90.00	85.00	94.44	4.50	90.00	0.00	0.00	0.00	90.00	0.00	0.00	0.00	90.00	0.00	0.00	0.00
