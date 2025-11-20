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
