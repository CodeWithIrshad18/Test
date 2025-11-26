using System.Globalization;

string begdaString = r["BEGDA"].ToString();

DateTime begda;

if (DateTime.TryParseExact(
        begdaString,
        "dd.MM.yyyy",
        CultureInfo.InvariantCulture,
        DateTimeStyles.None,
        out begda))
{
    room.AddCell(CenterCell(begda.ToString("MM.dd.yyyy")));
}
else
{
    room.AddCell(CenterCell(begdaString)); // fallback
}




STAY_DAYS	AMT_DEDUCT	GSTHOUSE_LOC_ID	GSTHOUSE_ID	BEGDA	ENDDA	ROOM_NO1	ROOM_NO2
2	1740	LC103	3001	19.12.2025	20.12.2025	204	
2	1740	LC103	3001	20.12.2025	21.12.2025	204	


System.FormatException: 'String was not recognized as a valid DateTime.'


         foreach (DataRow r in booking.Rows)
            {
                string displayRoom1 = r["ROOM_NO1"] + " - " + GetRoomTypeDescription(
                GetRoomTypeFromOracle(r["GSTHOUSE_ID"].ToString(), r["GSTHOUSE_LOC_ID"].ToString(), r["ROOM_NO1"].ToString()));

                string displayRoom2 = r["ROOM_NO2"] + " - " + GetRoomTypeDescription(
                GetRoomTypeFromOracle(r["GSTHOUSE_ID"].ToString(), r["GSTHOUSE_LOC_ID"].ToString(), r["ROOM_NO2"].ToString()));

                room.AddCell(CenterCell(Convert.ToDateTime(r["BEGDA"]).ToString("MM.dd.yyyy")));
                room.AddCell(CenterCell(displayRoom1));
                room.AddCell(CenterCell(displayRoom2));
            }
