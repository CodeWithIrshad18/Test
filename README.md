i have this 3 query 
select * from App_Wo_Nil where WO_NO = '4700024406' and NO_WORK = 'Temporary' and TEMPORARY_YEAR = '2025' and TEMPORARY_MONTH = '1'

select * from App_Wo_Nil where  convert(int, TEMPORARY_YEAR+ FORMAT(CONVERT(INT,CLOSER_DATE), '00'))<='202501' and NO_WORK='Permanent'
and WO_NO = '4700015075'


select * from APP_RECOGNIZED_WO 

1.i want to add these  if the process month and year has Temporary in No Work then Yes otherwise No
2. same for permanent
3. if the active workorder exist then show recognize on this table APP_RECOGNIZED_WO then show yes

