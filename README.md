this query return the LocId and GuestHouseID previous is wrong  

 public DataTable GetHeaderDetails(string pernr, string receiptNo)
        {
            string sql = @"select Distinct BD.GSTHOUSE_LOC_ID,BD.GSTHOUSE_ID,
                   FD.BEGDA,FD.ENDDA,BD.STAY_DAYS,BD.ROOM_NO1,BD.ROOM_NO2,BD.AMT_DEDUCT,
                   FD.NAME,FD.RELATION_CODE,FD.AGE,FD.GENDER_CODE 
                   from CTDRDB.T_BOOKING_DETAILS BD
                   left join CTDRDB.t_family_dtl FD 
                   on BD.RECEIPT_NO = FD.RECEIPT_NO
                   where BD.PERNR = :pernr 
                   and BD.RECEIPT_NO = :Recpt";

            List<OracleParameter> param = new List<OracleParameter>()
    {
        new OracleParameter("pernr", pernr),
        new OracleParameter("Recpt", receiptNo)
    };

            return OracleExecuteQuery(sql, param);
        }
