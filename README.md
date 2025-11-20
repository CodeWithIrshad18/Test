public string GenerateBookingPDF(string receiptNo, string perno, string fromdt, string Todt,
                                 string location, string hotel, string empName)
{
    string folderpath = @"C:\Cybersoft_Doc\SCH";
    string pdfPath = Path.Combine(folderpath, $"Permit_{receiptNo}.pdf");

    Document doc = new Document(PageSize.A4, 40, 40, 40, 40);
    PdfWriter.GetInstance(doc, new FileStream(pdfPath, FileMode.Create));
    doc.Open();

    // ---------------- HEADER LOGOS ----------------
    PdfPTable headerTbl = new PdfPTable(3);
    headerTbl.WidthPercentage = 100;
    float[] headerWidths = { 25, 50, 25 };
    headerTbl.SetWidths(headerWidths);

    iTextSharp.text.Image logo1 = iTextSharp.text.Image.GetInstance(@"C:\Cybersoft_Doc\Images\logo1.jpg");
    logo1.ScaleAbsolute(90, 35);

    iTextSharp.text.Image logo2 = iTextSharp.text.Image.GetInstance(@"C:\Cybersoft_Doc\Images\logo2.jpg");
    logo2.ScaleAbsolute(70, 50);

    PdfPCell leftLogo = new PdfPCell(logo1) { Border = 0, HorizontalAlignment = Element.ALIGN_LEFT };
    PdfPCell rightLogo = new PdfPCell(logo2) { Border = 0, HorizontalAlignment = Element.ALIGN_RIGHT };

    Font titleFont = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 14);
    PdfPCell titleCell = new PdfPCell(new Phrase("Permit for Accommodation at Holiday Home", titleFont))
    {
        Border = 0,
        HorizontalAlignment = Element.ALIGN_CENTER,
        VerticalAlignment = Element.ALIGN_MIDDLE
    };

    headerTbl.AddCell(leftLogo);
    headerTbl.AddCell(titleCell);
    headerTbl.AddCell(rightLogo);
    doc.Add(headerTbl);

    doc.Add(new Paragraph("\n"));

    // ---------------- BASIC DETAILS ----------------
    Font normal = FontFactory.GetFont(FontFactory.HELVETICA, 10);
    Font bold = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10);

    PdfPTable detailTbl = new PdfPTable(2);
    detailTbl.WidthPercentage = 100;
    float[] detailWidths = { 50, 50 };
    detailTbl.SetWidths(detailWidths);

    detailTbl.AddCell(CreateCell($"Name: {empName}", normal));
    detailTbl.AddCell(CreateCell($"Personal No.: {perno}", normal));

    detailTbl.AddCell(CreateCell($"Location: {location}", normal));
    detailTbl.AddCell(CreateCell($"Guest House: {hotel}", normal));

    detailTbl.AddCell(CreateCell($"Check-In Date: {fromdt}", normal));
    detailTbl.AddCell(CreateCell($"Check-Out Date: {Todt}", normal));

    doc.Add(detailTbl);

    doc.Add(new Paragraph("\nROOM DETAILS\n", bold));

    // ---------------- ROOM DETAILS ----------------
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

    // ---------------- FAMILY DETAILS ----------------
    doc.Add(new Paragraph("\nFAMILY DETAILS\n", bold));

    PdfPTable famTbl = new PdfPTable(4);
    famTbl.WidthPercentage = 100;

    famTbl.AddCell(Header("Name"));
    famTbl.AddCell(Header("Relationship"));
    famTbl.AddCell(Header("Age"));
    famTbl.AddCell(Header("Gender"));

    DataTable dtFamily = GetFamilyDetails(perno, receiptNo);

    int childCount = 0;
    int adultCount = 0;

    foreach (DataRow f in dtFamily.Rows)
    {
        string relation = MapRelation(f["RELATION_CODE"].ToString());
        string gender = MapGender(f["GENDER_CODE"].ToString());
        int age = Convert.ToInt32(f["AGE"]);

        if (age < 18) childCount++;
        else adultCount++;

        famTbl.AddCell(f["NAME"].ToString());
        famTbl.AddCell(relation);
        famTbl.AddCell(age.ToString());
        famTbl.AddCell(gender);
    }

    doc.Add(famTbl);

    // ---------------- MEMBER DETAILS ----------------
    doc.Add(new Paragraph("\nMEMBER DETAILS\n", bold));

    PdfPTable memTbl = new PdfPTable(3);
    memTbl.WidthPercentage = 100;

    memTbl.AddCell(Header($"Child: {childCount}"));
    memTbl.AddCell(Header($"Adult: {adultCount}"));
    memTbl.AddCell(Header($"Total No. of persons: {dtFamily.Rows.Count}"));

    doc.Add(memTbl);

    doc.Add(new Paragraph("\n"));

    // ---------------- WITH REGARDS BLOCK ----------------
    PdfPTable regardTbl = new PdfPTable(2);
    regardTbl.WidthPercentage = 100;
    regardTbl.SetWidths(new float[] { 50, 50 });

    regardTbl.AddCell(new PdfPCell(new Phrase("With Regards,\nArea Manager, Employment Bureau\n(Group HR/IR)", normal))
    {
        Border = 0,
        HorizontalAlignment = Element.ALIGN_LEFT
    });

    regardTbl.AddCell(new PdfPCell(new Phrase("Contact Person for Holiday Home\nBIBHU RANJAN MISHRA, 9827104012", bold))
    {
        Border = 0,
        HorizontalAlignment = Element.ALIGN_RIGHT
    });

    doc.Add(regardTbl);

    // ---------------- TERMS & CONDITIONS ----------------
    doc.Add(new Paragraph("\n*** SINCE IT IS AN ELECTRONICALLY GENERATED PERMIT SO SIGNATURE IS NOT REQUIRED ***\n", bold));

    doc.Add(new Paragraph("\nTerms And Conditions\n", bold));
    doc.Add(new Paragraph(
        "* The employee must carry ID card & permit.\n" +
        "* Occupancy strictly as per permit.\n" +
        "* Alcohol is prohibited.\n" +
        "* Cancellation must be done 15 days before.\n" +
        "* Late checkout attracts retention charges.\n" +
        "* Any damage to property must be compensated.\n",
        normal
    ));

    doc.Close();
    return pdfPath;
}

// ---------------- HELPER METHODS ----------------

private PdfPCell CreateCell(string text, Font font)
{
    return new PdfPCell(new Phrase(text, font)) { Border = 0 };
}

private PdfPCell Header(string text)
{
    return new PdfPCell(new Phrase(text, FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10)))
    {
        BackgroundColor = BaseColor.WHITE,
        HorizontalAlignment = Element.ALIGN_LEFT
    };
}

private string MapRelation(string code)
{
    switch (code)
    {
        case "9H": return "Self";
        case "1": return "Spouse";
        case "2": return "Child";
        case "11": return "Father";
        case "12": return "Mother";
        case "9A": return "Brother";
        case "9B": return "Sister";
        default: return code;
    }
}

private string MapGender(string code)
{
    return code == "1" ? "Male" :
           code == "2" ? "Female" :
           code;
}







string GetRelation(string code)
{
    switch (code)
    {
        case "9H": return "Self";
        case "1": return "Spouse";
        case "2": return "Child";
        case "11": return "Father";
        case "12": return "Mother";
        case "9A": return "Brother";
        case "9B": return "Sister";
        default: return "";
    }
}

string relation = GetRelation(row["RELATION_CODE"].ToString());

string GetGender(string code)
{
    switch (code)
    {
        case "1": return "Male";
        case "2": return "Female";
        default: return "";
    }
}

string gender = GetGender(row["GENDER_CODE"].ToString());

int childCount = 0;
int adultCount = 0;

foreach (DataRow row in ds.Tables[0].Rows)
{
    int age = Convert.ToInt32(row["AGE"]);

    if (age < 18)
        childCount++;
    else
        adultCount++;
}


int totalPersons = childCount + adultCount;

PdfPTable memberTable = new PdfPTable(3);
memberTable.WidthPercentage = 100;
memberTable.SetWidths(new float[] { 33, 33, 34 });

Font boldFont = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10);
Font normalFont = FontFactory.GetFont(FontFactory.HELVETICA, 10);

// Header
PdfPCell header = new PdfPCell(new Phrase("MEMBER DETAILS", boldFont));
header.Colspan = 3;
header.BackgroundColor = BaseColor.WHITE;
header.Padding = 5;
memberTable.AddCell(header);

// Child
memberTable.AddCell(new Phrase("Child: " + childCount, boldFont));

// Adult
memberTable.AddCell(new Phrase("Adult: " + adultCount, boldFont));

// Total persons
memberTable.AddCell(new Phrase("Total No. of persons: " + totalPersons, boldFont));

// Charges
PdfPCell charges = new PdfPCell(new Phrase("Total Charges: Rs." + totalCharges, boldFont));
charges.Colspan = 3;
charges.Padding = 5;
memberTable.AddCell(charges);

document.Add(memberTable);


Paragraph regardsLeft = new Paragraph();
regardsLeft.Add(new Phrase("With Regards,\n", normalFont));
regardsLeft.Add(new Phrase("Area Manager, Employment Bureau\n", normalFont));
regardsLeft.Add(new Phrase("(Group HR/IR)\n\n", boldFont));

Paragraph regardsRight = new Paragraph();
regardsRight.Alignment = Element.ALIGN_RIGHT;
regardsRight.Add(new Phrase("Contact Person for Holiday Home\n", boldFont));
regardsRight.Add(new Phrase(contactPersonName + ", " + contactPersonMobile, normalFont));

PdfPTable regardsTable = new PdfPTable(2);
regardsTable.WidthPercentage = 100;
regardsTable.SetWidths(new float[] { 50, 50 });

regardsTable.AddCell(new PdfPCell(regardsLeft) { Border = Rectangle.NO_BORDER });
regardsTable.AddCell(new PdfPCell(regardsRight) { Border = Rectangle.NO_BORDER });

document.Add(regardsTable);



---for relationship 

=Switch(Fields!RELATION_CODE.Value="9H","Self",
Fields!RELATION_CODE.Value="1","Spouse",
Fields!RELATION_CODE.Value="2","Child",
Fields!RELATION_CODE.Value="11","Father",
Fields!RELATION_CODE.Value="12","Mother",
Fields!RELATION_CODE.Value="9A","Brother",
Fields!RELATION_CODE.Value="9B","Sister")

----for Gender

=Switch(Fields!GENDER_CODE.Value="1","Male",
Fields!GENDER_CODE.Value="2","Female")

----for child and Adult

=count(iif(Fields!AGE.Value<18,1,Nothing), "DataSet2")


=count(iif(Fields!AGE.Value>=18,1,Nothing),"DataSet2")
