public string GetRoomTypeFromOracle(string guestHouseId, string locId, string roomNo)
{
    string sql = @"SELECT DISTINCT ROOM_TYPE 
                   FROM CTDRDB.T_GHSE_DTLS 
                   WHERE GSTHOUSE_ID = :GSTHOUSE_ID 
                   AND LOC_ID = :GSTHOUSE_LOC_ID 
                   AND ROOM_NO = :ROOM_NO";

    List<OracleParameter> param = new List<OracleParameter>()
    {
        new OracleParameter("GSTHOUSE_ID", guestHouseId),
        new OracleParameter("GSTHOUSE_LOC_ID", locId),
        new OracleParameter("ROOM_NO", roomNo)
    };

    DataTable dt = OracleExecuteQuery(sql, param);

    return dt.Rows.Count > 0 ? dt.Rows[0]["ROOM_TYPE"].ToString() : "";
}
public string GetRoomTypeDescription(string roomType)
{
    string desc = "";
    string conStr = "YOUR_SQL_SERVER_CONNECTION_STRING";

    using (SqlConnection con = new SqlConnection(conStr))
    {
        string sql = @"SELECT RoomTypeDescription 
                       FROM App_HDH_RoomTypeMaster 
                       WHERE RoomType = @RoomType";

        using (SqlCommand cmd = new SqlCommand(sql, con))
        {
            cmd.Parameters.AddWithValue("@RoomType", roomType);
            con.Open();
            object result = cmd.ExecuteScalar();
            if (result != null)
                desc = result.ToString();
        }
    }
    return desc;
}

DataTable dtHeader = GetHeaderDetails(perno, receiptNo);

foreach (DataRow r in dtHeader.Rows)
{
    string guestHouseId = r["GSTHOUSE_ID"].ToString();
    string locId = r["GSTHOUSE_LOC_ID"].ToString();

    string roomNo1 = r["ROOM_NO1"].ToString();
    string roomNo2 = r["ROOM_NO2"].ToString();

    string roomType1 = GetRoomTypeFromOracle(guestHouseId, locId, roomNo1);
    string roomType2 = GetRoomTypeFromOracle(guestHouseId, locId, roomNo2);

    string desc1 = GetRoomTypeDescription(roomType1);
    string desc2 = GetRoomTypeDescription(roomType2);

    roomTbl.AddCell(Convert.ToDateTime(r["BEGDA"]).ToString("dd-MM-yyyy"));
    roomTbl.AddCell(desc1);  // ✅ Proper description
    roomTbl.AddCell(desc2);
}




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
