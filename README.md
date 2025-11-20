public class BookingHeader
{
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
    public string ReceiptNo { get; set; }
    public string TotalCharges { get; set; }
    public string AdultCount { get; set; }
    public string ChildCount { get; set; }
}

public class RoomDetail
{
    public string Date { get; set; }
    public string Room1 { get; set; }
    public string Room2 { get; set; }
}

public class FamilyDetail
{
    public string Name { get; set; }
    public string Relation { get; set; }
    public string Age { get; set; }
    public string Gender { get; set; }
}

public static BookingHeader GetHeader(string receiptNo)
{
    string query = @"
        SELECT NAME, PERSONAL_NO, DEPARTMENT, MOBILE, LOCATION, GUEST_HOUSE,
               CHECKIN_DATE, CHECKIN_TIME, CHECKOUT_DATE, CHECKOUT_TIME,
               DURATION, RECEIPT_NO, TOTAL_CHARGES, ADULT_COUNT, CHILD_COUNT
        FROM HOLIDAY_HOME_HEADER
        WHERE RECEIPT_NO = :p1";

    using (var con = new OracleConnection("your_connection"))
    using (var cmd = new OracleCommand(query, con))
    {
        cmd.Parameters.Add(new OracleParameter("p1", receiptNo));

        con.Open();
        var dr = cmd.ExecuteReader();

        BookingHeader h = new BookingHeader();
        if (dr.Read())
        {
            h.Name = dr["NAME"].ToString();
            h.PersonalNo = dr["PERSONAL_NO"].ToString();
            h.Department = dr["DEPARTMENT"].ToString();
            h.Mobile = dr["MOBILE"].ToString();
            h.Location = dr["LOCATION"].ToString();
            h.GuestHouse = dr["GUEST_HOUSE"].ToString();
            h.CheckInDate = dr["CHECKIN_DATE"].ToString();
            h.CheckInTime = dr["CHECKIN_TIME"].ToString();
            h.CheckOutDate = dr["CHECKOUT_DATE"].ToString();
            h.CheckOutTime = dr["CHECKOUT_TIME"].ToString();
            h.Duration = dr["DURATION"].ToString();
            h.ReceiptNo = dr["RECEIPT_NO"].ToString();
            h.TotalCharges = dr["TOTAL_CHARGES"].ToString();
            h.AdultCount = dr["ADULT_COUNT"].ToString();
            h.ChildCount = dr["CHILD_COUNT"].ToString();
        }
        return h;
    }
}

public static List<RoomDetail> GetRoomDetails(string receiptNo)
{
    string query = @"
        SELECT DATE, ROOM1, ROOM2 
        FROM HOLIDAY_HOME_ROOMS
        WHERE RECEIPT_NO = :p1
        ORDER BY DATE";

    List<RoomDetail> list = new List<RoomDetail>();

    using (var con = new OracleConnection("your_connection"))
    using (var cmd = new OracleCommand(query, con))
    {
        cmd.Parameters.Add(new OracleParameter("p1", receiptNo));
        con.Open();
        var dr = cmd.ExecuteReader();
        while (dr.Read())
        {
            list.Add(new RoomDetail
            {
                Date = dr["DATE"].ToString(),
                Room1 = dr["ROOM1"].ToString(),
                Room2 = dr["ROOM2"].ToString()
            });
        }
    }
    return list;
}

public static List<FamilyDetail> GetFamilyDetails(string receiptNo)
{
    string query = @"
        SELECT NAME, RELATION, AGE, GENDER
        FROM HOLIDAY_HOME_MEMBERS
        WHERE RECEIPT_NO = :p1";

    List<FamilyDetail> list = new List<FamilyDetail>();

    using (var con = new OracleConnection("your_connection"))
    using (var cmd = new OracleCommand(query, con))
    {
        cmd.Parameters.Add(new OracleParameter("p1", receiptNo));
        con.Open();

        var dr = cmd.ExecuteReader();
        while (dr.Read())
        {
            list.Add(new FamilyDetail
            {
                Name = dr["NAME"].ToString(),
                Relation = dr["RELATION"].ToString(),
                Age = dr["AGE"].ToString(),
                Gender = dr["GENDER"].ToString()
            });
        }
    }
    return list;
}


using System;
using System.Collections.Generic;
using System.IO;
using iTextSharp.text;
using iTextSharp.text.pdf;

public class HolidayHomePdfGenerator
{
    public static byte[] GeneratePDF(BookingHeader h, List<RoomDetail> rooms, List<FamilyDetail> members)
    {
        using (MemoryStream ms = new MemoryStream())
        {
            Document doc = new Document(PageSize.A4, 35, 35, 35, 35);
            PdfWriter writer = PdfWriter.GetInstance(doc, ms);
            doc.Open();

            Font titleFont = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 14);
            Font bold = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10);
            Font normal = FontFactory.GetFont(FontFactory.HELVETICA, 10);
            Font red = new Font(Font.FontFamily.HELVETICA, 9, Font.BOLD, BaseColor.RED);

            // ─────────────────────────────────────────────────
            // HEADER TITLE
            // ─────────────────────────────────────────────────
            Paragraph p = new Paragraph("Permit for Accommodation at Holiday Home", titleFont);
            p.Alignment = Element.ALIGN_CENTER;
            doc.Add(p);

            doc.Add(new Paragraph("\n"));

            PdfPTable header = new PdfPTable(2);
            header.WidthPercentage = 100;

            header.AddCell(Cell("Name: " + h.Name, normal));
            header.AddCell(Cell("Personal No: " + h.PersonalNo, normal));

            header.AddCell(Cell("Department: " + h.Department, normal));
            header.AddCell(Cell("Mobile: " + h.Mobile, normal));

            header.AddCell(Cell("Location: " + h.Location, normal));
            header.AddCell(Cell("Guest House: " + h.GuestHouse, normal));

            header.AddCell(Cell("Check-In: " + h.CheckInDate + " " + h.CheckInTime, normal));
            header.AddCell(Cell("Check-Out: " + h.CheckOutDate + " " + h.CheckOutTime, normal));

            header.AddCell(Cell("Duration: " + h.Duration + " Days", normal));
            header.AddCell(Cell("Receipt No: " + h.ReceiptNo, normal));

            doc.Add(header);

            doc.Add(new Paragraph("\n"));

            // ─────────────────────────────────────────────────
            // ROOM DETAILS TABLE
            // ─────────────────────────────────────────────────
            PdfPTable t1 = new PdfPTable(3);
            t1.WidthPercentage = 100;
            t1.SetWidths(new float[] { 25, 40, 35 });

            t1.AddCell(Header("Date"));
            t1.AddCell(Header("Room No - 1"));
            t1.AddCell(Header("Room No - 2"));

            foreach (var r in rooms)
            {
                t1.AddCell(Cell(r.Date, normal));
                t1.AddCell(Cell(r.Room1, normal));
                t1.AddCell(Cell(r.Room2, normal));
            }

            doc.Add(t1);

            doc.Add(new Paragraph("\n"));

            // ─────────────────────────────────────────────────
            // FAMILY DETAILS TABLE
            // ─────────────────────────────────────────────────
            PdfPTable t2 = new PdfPTable(4);
            t2.WidthPercentage = 100;
            t2.SetWidths(new float[] { 40, 20, 20, 20 });

            t2.AddCell(Header("Name"));
            t2.AddCell(Header("Relation"));
            t2.AddCell(Header("Age"));
            t2.AddCell(Header("Gender"));

            foreach (var m in members)
            {
                t2.AddCell(Cell(m.Name, normal));
                t2.AddCell(Cell(m.Relation, normal));
                t2.AddCell(Cell(m.Age, normal));
                t2.AddCell(Cell(m.Gender, normal));
            }

            doc.Add(t2);

            doc.Add(new Paragraph("\n"));

            // ─────────────────────────────────────────────────
            // SUMMARY SECTION
            // ─────────────────────────────────────────────────
            PdfPTable summary = new PdfPTable(4);
            summary.WidthPercentage = 100;

            summary.AddCell(Cell("Adults: " + h.AdultCount, bold));
            summary.AddCell(Cell("Children: " + h.ChildCount, bold));
            summary.AddCell(Cell("Total Persons: " + (int.Parse(h.AdultCount) + int.Parse(h.ChildCount)), bold));
            summary.AddCell(Cell("Total Charges: Rs. " + h.TotalCharges, bold));

            doc.Add(summary);

            doc.Add(new Paragraph("\n"));

            // ─────────────────────────────────────────────────
            // TERMS – Red Text
            // ─────────────────────────────────────────────────
            Paragraph t = new Paragraph(
                "Note: Once accommodation is allotted, the respective employee has to ensure proper upkeep ..." +
                " [Your screenshot terms text continues here exactly]",
                red);
            t.Alignment = Element.ALIGN_LEFT;
            doc.Add(t);

            doc.Close();
            return ms.ToArray();
        }
    }

    private static PdfPCell Header(string text)
    {
        Font f = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10);
        PdfPCell c = new PdfPCell(new Phrase(text, f));
        c.BackgroundColor = new BaseColor(230, 230, 230);
        c.Padding = 5;
        return c;
    }

    private static PdfPCell Cell(string text, Font f)
    {
        PdfPCell c = new PdfPCell(new Phrase(text, f));
        c.Padding = 5;
        return c;
    }
}

var header = GetHeader(receiptNo);
var rooms = GetRoomDetails(receiptNo);
var family = GetFamilyDetails(receiptNo);

byte[] pdf = HolidayHomePdfGenerator.GeneratePDF(header, rooms, family);

// Send email with pdf as attachment
SendMail(toEmail, body, subject, pdf);



