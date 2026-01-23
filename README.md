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
   AND tsd.PeriodicityTransactionID = 'JAN'   -- 🔥 January Target

LEFT JOIN App_PeriodicityTransaction_NOPR pt
    ON pt.PeriodicityName = 'JAN'
   AND pt.PeriodicityID = ts.PeriodicityID

LEFT JOIN App_KPIDetails_NOPR kd
    ON kd.KPIID = km.ID
   AND kd.PeriodTransactionID = pt.ID         -- 🔥 January Actual

LEFT JOIN App_UOM_NOPR u
    ON km.UnitID = u.ID

WHERE km.Department = 'Human Resource & Industrial Relations'   -- 🔥 HR only

ORDER BY km.KPICode;






DECLARE @MonthName VARCHAR(10) = 'JAN';

SELECT  
    km.KPICode,
    km.KPIDetails AS Areas,
    km.Department,
    km.Division,

    t.Marker,                 -- 1 = ↑ , 0 = ↓
    u.UnitCode AS UoM,
    ts.Weightage,

    tsd.TargetValue AS MonthlyTarget,
    kd.Value AS Actual,

    -- Achievement %
    CASE 
        WHEN t.Marker = 1 AND tsd.TargetValue > 0 AND kd.Value IS NOT NULL
            THEN ROUND((kd.Value / tsd.TargetValue) * 100, 2)
        WHEN t.Marker = 0 AND kd.Value > 0
            THEN ROUND((tsd.TargetValue / kd.Value) * 100, 2)
        ELSE NULL
    END AS AchievementPercent,

    -- Weighted %
    CASE 
        WHEN t.Marker = 1 AND tsd.TargetValue > 0 AND kd.Value IS NOT NULL
            THEN ROUND(((kd.Value / tsd.TargetValue) * 100) * ts.Weightage / 100, 2)
        WHEN t.Marker = 0 AND kd.Value > 0
            THEN ROUND(((tsd.TargetValue / kd.Value) * 100) * ts.Weightage / 100, 2)
        ELSE NULL
    END AS WeightedPercent

FROM App_KPIMaster_NOPR km

LEFT JOIN App_TargetSetting_NOPR ts
    ON ts.KPIID = km.ID

LEFT JOIN App_TargetSettingDetails_NOPR tsd
    ON tsd.MasterID = ts.ID

LEFT JOIN App_PeriodicityTransaction_NOPR pt
    ON pt.PeriodicityName = tsd.PeriodicityTransactionID
   AND pt.PeriodicityID = ts.PeriodicityID
   AND pt.PeriodicityName = @MonthName   -- 🔥 Month filter

LEFT JOIN App_KPIDetails_NOPR kd
    ON kd.KPIID = km.ID
   AND kd.PeriodTransactionID = pt.ID

LEFT JOIN App_UOM_NOPR u
    ON km.UnitID = u.ID

LEFT JOIN App_TypeofKPI_NOPR t
    ON km.TypeofKPIID = t.ID

ORDER BY km.KPICode;






TPR Achievement					Jan'25			
KPI#	Areas	Marker	UoM	Weightage	Monthly Target	Actual		
				100%				
1	Overall plant efficiency - RPH	1	%	10.00%	95.00	96.00	
2	Overall plant efficiency - Water Works	1	%	10.00%	88.00	87.00		
3	Overall plant efficiency - Water Distribution	1	%	10.00%	82.00			
4	Overall plant efficiency - Waste Water	1	%	10.00%	95.00	80.00		
5	Segregation at Source	1	%	10.00%	70.00	34.00	48.57%	4.86%
6	Compliance to PM - PSD (Overall upto 415 V Feeder Pillar including Earth Pit Main)	1	Nos	10.00%	447.00	431.00		
7	No.of HT & LT Breakdown	0	Nos	10.00%	13.00	12.00	108.33%	10.21%
8	TMH Electrical Supply & Maintenance - Customer Complaint within SLG	1	%	10.00%	100.00	99.80	
9	DDJ Compliance within SLG	1	%	10.00%	90.00	94.00	
10	NDJ Compliance within SLG	1	%	10.00%	90.00	87.00	

query : 

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
ORDER BY km.KPICode,pt.Sl_no;

Data : 


KPICode	KPIDetails	Division	Department	Section	TypeofKPI	UnitCode	PeriodicityName	Month	TargetValue	ActualValue	YTDValue
DP/001	Overall plant efficiency - Water Distribution	Water and Wastewater Services	Muncipal O&M	NULL	TPR	%	Monthly	NULL	NULL	NULL	NULL
SE/001	Overall Plant Efficiency -RPH	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Monthly	APR	97.00	96.0000	0.0000
SE/001	Overall Plant Efficiency -RPH	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Monthly	MAY	97.00	96.0000	0.0000
SE/001	Overall Plant Efficiency -RPH	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Monthly	JUN	97.00	96.0000	0.0000
SE/001	Overall Plant Efficiency -RPH	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Monthly	JUL	98.00	95.5100	0.0000
SE/001	Overall Plant Efficiency -RPH	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Monthly	AUG	98.00	95.5800	0.0000
SE/001	Overall Plant Efficiency -RPH	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Monthly	SEP	98.00	93.2500	0.0000
SE/001	Overall Plant Efficiency -RPH	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Monthly	OCT	98	94.8000	0.0000
SE/001	Overall Plant Efficiency -RPH	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Monthly	NOV	98	95.2100	0.0000
SE/001	Overall Plant Efficiency -RPH	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Monthly	DEC	98	95.1600	0.0000
SE/001	Overall Plant Efficiency -RPH	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Monthly	JAN	98	NULL	NULL
SE/001	Overall Plant Efficiency -RPH	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Monthly	FEB	98	NULL	NULL
SE/001	Overall Plant Efficiency -RPH	People Function	Human Resource & Industrial Relations	Industrial Relations	TPR	%	Monthly	MAR	98	NULL	NULL

i want my data in rows wise same like the reference image in 
