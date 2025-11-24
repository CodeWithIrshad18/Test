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
