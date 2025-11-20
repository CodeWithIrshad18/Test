public class HolidayHomeHeader
{
    public string ReceiptNo { get; set; }
    public string Name { get; set; }
    public string PersonalNo { get; set; }
    public string Department { get; set; }
    public string Mobile { get; set; }
    public string Location { get; set; }
    public string GuestHouse { get; set; }
    public string CheckInDate { get; set; }
    public string CheckInTime { get; set; }
    public string CheckOutDate { get; set; }
    public string CheckOutTime { get; set; }
    public string Duration { get; set; }
    public string RoomType { get; set; }
    public decimal TotalAmount { get; set; }
}

public class RoomDetail
{
    public string Date { get; set; }
    public string RoomNo1 { get; set; }
    public string RoomNo2 { get; set; }
}

public class FamilyDetail
{
    public string Name { get; set; }
    public string Relation { get; set; }
    public string Age { get; set; }
    public string Gender { get; set; }
}

public class HolidayHomeRepository
{
    private string _connString = ConfigurationManager.ConnectionStrings["connect_HDH"].ConnectionString;

    public (HolidayHomeHeader header, List<RoomDetail> rooms, List<FamilyDetail> family) 
        GetBookingData(string pernr, string receipt)
    {
        HolidayHomeHeader header = new HolidayHomeHeader();
        List<RoomDetail> rooms = new List<RoomDetail>();
        List<FamilyDetail> family = new List<FamilyDetail>();

        using (var con = new OracleConnection(_connString))
        {
            con.Open();

            //📌 HEADER + MAIN
            string sql = @"select Distinct BD.GSTHOUSE_LOC_ID,BD.GSTHOUSE_ID,FD.BEGDA,FD.ENDDA,
                           BD.STAY_DAYS,BD.ROOM_NO1,BD.ROOM_NO2,BD.AMT_DEDUCT,FD.NAME,FD.RELATION_CODE,
                           FD.AGE,FD.GENDER_CODE,BD.RECEIPT_NO,BD.PERNR,BD.MOB_NO,BD.LOCATION,BD.DEPARTMENT 
                           from CTDRDB.T_BOOKING_DETAILS BD 
                           left join CTDRDB.t_family_dtl FD on BD.RECEIPT_NO = FD.RECEIPT_NO 
                           where BD.PERNR =:pernr and BD.RECEIPT_NO=:Recpt";

            using (var cmd = new OracleCommand(sql, con))
            {
                cmd.Parameters.Add(new OracleParameter("pernr", pernr));
                cmd.Parameters.Add(new OracleParameter("Recpt", receipt));

                using (var dr = cmd.ExecuteReader())
                {
                    if (dr.Read())
                    {
                        header.ReceiptNo = dr["RECEIPT_NO"].ToString();
                        header.PersonalNo = dr["PERNR"].ToString();
                        header.Name = dr["NAME"].ToString();
                        header.Department = dr["DEPARTMENT"].ToString();
                        header.Mobile = dr["MOB_NO"].ToString();
                        header.Location = dr["LOCATION"].ToString();

                        header.CheckInDate = Convert.ToDateTime(dr["BEGDA"]).ToString("dd.MM.yyyy");
                        header.CheckOutDate = Convert.ToDateTime(dr["ENDDA"]).ToString("dd.MM.yyyy");
                        header.Duration = dr["STAY_DAYS"].ToString();

                        header.GuestHouse = dr["GSTHOUSE_ID"].ToString();
                    }
                }
            }

            //📌 ROOM TYPE
            string sqlRoomType = @"SELECT distinct ROOM_TYPE FROM CTDRDB.T_GHSE_DTLS 
                                   where GSTHOUSE_ID= :GSTHOUSE_ID and LOC_ID=:GSTHOUSE_LOC_ID";

            using (var cmd = new OracleCommand(sqlRoomType, con))
            {
                cmd.Parameters.Add(new OracleParameter("GSTHOUSE_ID", header.GuestHouse));
                cmd.Parameters.Add(new OracleParameter("GSTHOUSE_LOC_ID", header.Location));

                header.RoomType = Convert.ToString(cmd.ExecuteScalar());
            }

            //📌 ROOM DETAILS
            using (var cmd = new OracleCommand(sql_RoomDetails, con))
            {
                cmd.Parameters.Add(new OracleParameter("pernr", pernr));
                cmd.Parameters.Add(new OracleParameter("Recpt", receipt));

                using (var dr = cmd.ExecuteReader())
                {
                    while (dr.Read())
                    {
                        rooms.Add(new RoomDetail
                        {
                            Date = Convert.ToDateTime(dr["BEGDA"]).ToString("dd.MM.yyyy"),
                            RoomNo1 = dr["ROOM_NO1"].ToString(),
                            RoomNo2 = dr["ROOM_NO2"].ToString(),
                        });

                        header.TotalAmount += Convert.ToDecimal(dr["AMT_DEDUCT"]);
                    }
                }
            }

            //📌 FAMILY DETAILS
            using (var cmd = new OracleCommand(sql_FamilyDetails, con))
            {
                cmd.Parameters.Add(new OracleParameter("pernr", pernr));
                cmd.Parameters.Add(new OracleParameter("Recpt", receipt));

                using (var dr = cmd.ExecuteReader())
                {
                    while (dr.Read())
                    {
                        family.Add(new FamilyDetail
                        {
                            Name = dr["NAME"].ToString(),
                            Relation = dr["RELATION_CODE"].ToString(),
                            Age = dr["AGE"].ToString(),
                            Gender = dr["GENDER_CODE"].ToString()
                        });
                    }
                }
            }
        }

        return (header, rooms, family);
    }
}

using iTextSharp.text;
using iTextSharp.text.pdf;
using System.Collections.Generic;
using System.IO;

public static class HolidayHomePdfGenerator
{
    public static byte[] Generate(HolidayHomeHeader h, List<RoomDetail> rooms, List<FamilyDetail> family)
    {
        using (var ms = new MemoryStream())
        {
            //A4 PDF
            Document doc = new Document(PageSize.A4, 35, 35, 35, 35);
            PdfWriter.GetInstance(doc, ms);
            doc.Open();

            var bold = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10);
            var normal = FontFactory.GetFont(FontFactory.HELVETICA, 10);
            var title = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 14);

            //HEADER TITLE
            Paragraph p = new Paragraph("Permit for Accommodation at Holiday Home", title);
            p.Alignment = Element.ALIGN_CENTER;
            doc.Add(p);

            doc.Add(new Paragraph("Receipt No: " + h.ReceiptNo, bold));
            doc.Add(new Paragraph("\n", normal));

            //DETAILS TABLE (2 columns)
            PdfPTable t = new PdfPTable(2);
            t.WidthPercentage = 100;
            t.SetWidths(new float[] { 50, 50 });

            AddCell(t, "Name: " + h.Name, normal);
            AddCell(t, "Personal No: " + h.PersonalNo, normal);
            AddCell(t, "Department: " + h.Department, normal);
            AddCell(t, "Mobile: " + h.Mobile, normal);
            AddCell(t, "Location: " + h.Location, normal);
            AddCell(t, "Guest House: " + h.GuestHouse + " (" + h.RoomType + ")", normal);
            AddCell(t, "Check-In: " + h.CheckInDate, normal);
            AddCell(t, "Check-Out: " + h.CheckOutDate, normal);
            AddCell(t, "Duration (Days): " + h.Duration, normal);
            AddCell(t, "Total Charges (Rs): " + h.TotalAmount, normal);

            doc.Add(t);
            doc.Add(new Paragraph("\n", normal));

            //ROOM DETAILS TABLE
            PdfPTable rt = new PdfPTable(3);
            rt.WidthPercentage = 100;
            rt.SetWidths(new float[] { 33, 33, 33 });

            AddHeader(rt, "Date");
            AddHeader(rt, "Room No 1");
            AddHeader(rt, "Room No 2");

            foreach (var r in rooms)
            {
                AddCell(rt, r.Date);
                AddCell(rt, r.RoomNo1);
                AddCell(rt, r.RoomNo2);
            }

            doc.Add(rt);

            doc.Add(new Paragraph("\nFamily Details", bold));

            //FAMILY DETAILS
            PdfPTable ft = new PdfPTable(4);
            ft.WidthPercentage = 100;
            ft.SetWidths(new float[] { 40, 20, 20, 20 });

            AddHeader(ft, "Name");
            AddHeader(ft, "Relation");
            AddHeader(ft, "Age");
            AddHeader(ft, "Gender");

            foreach (var f in family)
            {
                AddCell(ft, f.Name);
                AddCell(ft, f.Relation);
                AddCell(ft, f.Age);
                AddCell(ft, f.Gender);
            }

            doc.Add(ft);

            doc.Add(new Paragraph("\nTerms & Conditions", bold));

            string tc = @"1. The permit is valid only for the dates indicated above.
2. Guests must carry valid ID proof.
3. Any damage to property will be chargeable.
4. The permit must be produced on arrival at the guest house.";

            doc.Add(new Paragraph(tc, normal));

            doc.Close();
            return ms.ToArray();
        }
    }

    private static void AddCell(PdfPTable t, string text, Font f = null)
    {
        t.AddCell(new PdfPCell(new Phrase(text, f ?? FontFactory.GetFont(FontFactory.HELVETICA, 10))) { Padding = 5 });
    }

    private static void AddHeader(PdfPTable t, string text)
    {
        var cell = new PdfPCell(new Phrase(text, FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10)));
        cell.BackgroundColor = BaseColor.LIGHT_GRAY;
        cell.Padding = 5;
        t.AddCell(cell);
    }
}







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
