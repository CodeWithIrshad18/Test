string imgFolder = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot", "Images");

string leftLogoPath = Path.Combine(imgFolder, "logo1.jpg");
string rightLogoPath = Path.Combine(imgFolder, "logo3.png");

Image leftLogo = Image.GetInstance(leftLogoPath);
leftLogo.ScaleAbsolute(90, 70);

Image rightLogo = Image.GetInstance(rightLogoPath);
rightLogo.ScaleAbsolute(90, 70);


 PdfPTable header = new PdfPTable(3);
header.WidthPercentage = 100;
header.SetWidths(new float[] { 20, 60, 20 }); // keeps center text balanced

header.AddCell(new PdfPCell(leftLogo)
{
    Border = 0,
    HorizontalAlignment = Element.ALIGN_LEFT
});

header.AddCell(new PdfPCell(new Phrase("Permit for Accommodation at Holiday Home", titleFont))
{
    Border = 0,
    HorizontalAlignment = Element.ALIGN_CENTER,
    VerticalAlignment = Element.ALIGN_MIDDLE
});

header.AddCell(new PdfPCell(rightLogo)
{
    Border = 0,
    HorizontalAlignment = Element.ALIGN_RIGHT
});

// Add to document
doc.Add(header);

 
 
 Image leftLogo = Image.GetInstance(@"C:\Images\logo1.jpg");
            leftLogo.ScaleAbsolute(90, 70);

            Image rightLogo = Image.GetInstance(@"C:\Images\logo3.png");
            rightLogo.ScaleAbsolute(90, 70);

            header.AddCell(new PdfPCell(leftLogo) { Border = 0, HorizontalAlignment = Element.ALIGN_LEFT });
            header.AddCell(new PdfPCell(new Phrase("Permit for Accomodation at Holiday Home", titleFont))
            {
                Border = 0,
                HorizontalAlignment = Element.ALIGN_CENTER,
                VerticalAlignment = Element.ALIGN_MIDDLE
            });

            header.AddCell(new PdfPCell(rightLogo) { Border = 0, HorizontalAlignment = Element.ALIGN_RIGHT });

            doc.Add(header);
