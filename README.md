
public string GetRoomTypeFromOracle(string ghouseId, string locId, string roomNo)
{
    string sql = @"SELECT DISTINCT ROOM_TYPE 
                   FROM CTDRDB.T_GHSE_DTLS 
                   WHERE GSTHOUSE_ID = :GSTHOUSE_ID 
                   AND LOC_ID = :GSTHOUSE_LOC_ID 
                   AND ROOM_NO = :ROOM_NO";

    List<OracleParameter> param = new List<OracleParameter>()
    {
        new OracleParameter("GSTHOUSE_ID", ghouseId),
        new OracleParameter("GSTHOUSE_LOC_ID", locId),
        new OracleParameter("ROOM_NO", roomNo)
    };

    DataTable dt = OracleExecuteQuery(sql, param);

    return dt.Rows.Count > 0 ? dt.Rows[0]["ROOM_TYPE"].ToString() : "";
}

using System.Data.SqlClient;

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

            var result = cmd.ExecuteScalar();
            if (result != null)
                desc = result.ToString();
        }
    }

    return desc;
}

DataTable dtRooms = GetRoomDetails(perno, receiptNo);

foreach (DataRow r in dtRooms.Rows)
{
    string roomNo1 = r["ROOM_NO1"].ToString();
    string roomNo2 = r["ROOM_NO2"].ToString();

    string roomType1 = GetRoomTypeFromOracle(hotel, location, roomNo1);
    string roomType2 = GetRoomTypeFromOracle(hotel, location, roomNo2);

    string roomDesc1 = GetRoomTypeDescription(roomType1);
    string roomDesc2 = GetRoomTypeDescription(roomType2);

    roomTbl.AddCell(r["BEGDA"].ToString());
    roomTbl.AddCell(roomDesc1);   // ✅ Choice 1 description
    roomTbl.AddCell(roomDesc2);   // ✅ Choice 2 description
}





oracle-- this return type from Oracle sql 

 string sql2 = "SELECT distinct ROOM_TYPE FROM CTDRDB.T_GHSE_DTLS where GSTHOUSE_ID= :GSTHOUSE_ID and LOC_ID=:GSTHOUSE_LOC_ID and ROOM_NO=:ROOM_NO ";

sql server-- with Room no in Room Details section show RoomTypeDescription 

public DataSet Get_RoomTyp_Descrptn(string RoomTyp)
        {

            string strsql = "select RoomTypeDescription from App_HDH_RoomTypeMaster where RoomType=@RoomType";
            Dictionary<string, object> objParam = new Dictionary<string, object>();
            DataHelper dh = new DataHelper();
            objParam.Add("RoomType", RoomTyp);
            return dh.GetDataset(strsql, "App_HDH_RoomTypeMaster", objParam);

        }




 public DataTable GetRoomDetails(string pernr, string receiptNo)
        {
            string sql = @"select Distinct BD.BEGDA,BD.ROOM_NO1,BD.ROOM_NO2,BD.AMT_DEDUCT
                   from CTDRDB.T_BOOKING_DETAILS BD
                   left join CTDRDB.t_family_dtl FD 
                   on BD.RECEIPT_NO = FD.RECEIPT_NO
                   where BD.PERNR = :pernr 
                   and BD.RECEIPT_NO = :Recpt
                   order by BD.BEGDA";

            List<OracleParameter> param = new List<OracleParameter>()
    {
        new OracleParameter("pernr", pernr),
        new OracleParameter("Recpt", receiptNo)
    };

            return OracleExecuteQuery(sql, param);
        }

Pdf : 
   PdfPTable roomTbl = new PdfPTable(3);
            roomTbl.WidthPercentage = 100;
            roomTbl.AddCell(Header("Date"));
            roomTbl.AddCell(Header("Choice 1"));
            roomTbl.AddCell(Header("Choice 2"));

            DataTable dtRooms = GetRoomDetails(perno, receiptNo);
            foreach (DataRow r in dtRooms.Rows)
            {
                roomTbl.AddCell(r["BEGDA"].ToString());
                roomTbl.AddCell(r["ROOM_NO1"].ToString());
                roomTbl.AddCell(r["ROOM_NO2"].ToString());
            }
            doc.Add(roomTbl);
