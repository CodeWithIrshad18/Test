//
            string sql = "select Distinct BD.GSTHOUSE_LOC_ID,BD.GSTHOUSE_ID,FD.BEGDA,FD.ENDDA,BD.STAY_DAYS,BD.ROOM_NO1,BD.ROOM_NO2,BD.AMT_DEDUCT,FD.NAME,FD.RELATION_CODE,FD.AGE,FD.GENDER_CODE from CTDRDB.T_BOOKING_DETAILS BD left join CTDRDB.t_family_dtl FD on BD.RECEIPT_NO = FD.RECEIPT_NO where BD.PERNR =:pernr and BD.RECEIPT_NO=:Recpt";

//Room Details

            string sql_RoomDetails = "select Distinct BD.BEGDA,BD.ROOM_NO1,BD.ROOM_NO2,BD.AMT_DEDUCT from CTDRDB.T_BOOKING_DETAILS BD left join CTDRDB.t_family_dtl FD on BD.RECEIPT_NO = FD.RECEIPT_NO where BD.PERNR =:pernr and BD.RECEIPT_NO=:Recpt order by BD.BEGDA";

//Family Details

            string sql_FamilyDetails = "select Distinct FD.NAME,FD.RELATION_CODE,FD.AGE,FD.GENDER_CODE,FD.SNO from CTDRDB.T_BOOKING_DETAILS BD left join CTDRDB.t_family_dtl FD on BD.RECEIPT_NO = FD.RECEIPT_NO where BD.PERNR =:pernr and BD.RECEIPT_NO=:Recpt order by FD.SNO";



//Room Type
   string sql2 = "SELECT distinct ROOM_TYPE FROM CTDRDB.T_GHSE_DTLS where GSTHOUSE_ID= :GSTHOUSE_ID and LOC_ID=:GSTHOUSE_LOC_ID ";


//Connectionstring 


 <add name="connect_HDH" connectionString="Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=176.0.0.105)(PORT=1521))(CONNECT_DATA=(SID=cmdrdev)));User ID=CTDRTSUISL;Password=Jpeg#2025;" providerName="Oracle.ManagedDataAccess.Client"/>
