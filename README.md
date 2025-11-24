PdfPTable detail = new PdfPTable(4)
{
    WidthPercentage = 100,
    SpacingBefore = 5,
    SpacingAfter = 5
};
detail.SetWidths(new float[] { 18, 32, 18, 32 });

detail.AddCell(Label("Name")); detail.AddCell(Value(empName));
detail.AddCell(Label("Personal No.")); detail.AddCell(Value(perno));

detail.AddCell(Label("Department")); detail.AddCell(Value(dept));
detail.AddCell(Label("Mobile No.")); detail.AddCell(Value(phone));

detail.AddCell(Label("Location")); detail.AddCell(Value(location));
detail.AddCell(Label("Guest House")); detail.AddCell(Value(hotel));

detail.AddCell(Label("Check-in Date")); detail.AddCell(Value(fromdt));
detail.AddCell(Label("Check-out Date")); detail.AddCell(Value(Todt));

detail.AddCell(Label("Check-in Time")); detail.AddCell(Value(time.Rows[0]["CheckIn"].ToString()));
detail.AddCell(Label("Check-out Time")); detail.AddCell(Value(time.Rows[0]["CheckOut"].ToString()));

// ✅ Duration row aligned properly
detail.AddCell(Label("Duration"));
detail.AddCell(Value(Duration));
detail.AddCell(new PdfPCell(new Phrase("")) { Border = 0 });
detail.AddCell(new PdfPCell(new Phrase("")) { Border = 0 });

doc.Add(detail);




string imageFolder = Path.Combine(AppContext.BaseDirectory, "Images");

string leftLogoPath = Path.Combine(imageFolder, "logo1.jpg");
string rightLogoPath = Path.Combine(imageFolder, "logo3.png");

if (!File.Exists(leftLogoPath))
    throw new FileNotFoundException("Missing Logo: " + leftLogoPath);

if (!File.Exists(rightLogoPath))
    throw new FileNotFoundException("Missing Logo: " + rightLogoPath);

Image leftLogo = Image.GetInstance(leftLogoPath);
leftLogo.ScaleAbsolute(90, 70);

Image rightLogo = Image.GetInstance(rightLogoPath);
rightLogo.ScaleAbsolute(90, 70);

PdfPTable header = new PdfPTable(3)
{
    WidthPercentage = 100
};
header.SetWidths(new float[] { 20, 60, 20 });

header.AddCell(new PdfPCell(leftLogo) { Border = 0, HorizontalAlignment = Element.ALIGN_LEFT });

header.AddCell(new PdfPCell(new Phrase("Permit for Accommodation at Holiday Home", titleFont))
{
    Border = 0,
    HorizontalAlignment = Element.ALIGN_CENTER,
    VerticalAlignment = Element.ALIGN_MIDDLE
});

header.AddCell(new PdfPCell(rightLogo) { Border = 0, HorizontalAlignment = Element.ALIGN_RIGHT });

doc.Add(header);




DirectoryNotFoundException: Could not find a part of the path 'C:\Users\EWEPA7818A\Desktop\HDH Service 21-11-2025\HDH_BOOKING_CONFIRMATION\HDH_BOOKING_CONFIRMATION\bin\Debug\wwwroot\Images\logo1.jpg'.
