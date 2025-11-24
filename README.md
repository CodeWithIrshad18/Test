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
