public string GenerateBookingPDF(
    string receiptNo, string perno, string fromdt, string Todt,
    string location, string hotel, string empName)
{
    string folderpath = @"C:\Cybersoft_Doc\SCH";
    if (!Directory.Exists(folderpath))
        Directory.CreateDirectory(folderpath);

    string pdfPath = Path.Combine(folderpath, $"Permit_{receiptNo}.pdf");

    Document doc = new Document(PageSize.A4, 40, 40, 30, 30);
    PdfWriter.GetInstance(doc, new FileStream(pdfPath, FileMode.Create));
    doc.Open();

    // Load Fonts
    var titleFont = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 14);
    var subFont = FontFactory.GetFont(FontFactory.HELVETICA, 10);
    var boldFont = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10);
    var redFont = FontFactory.GetFont(FontFactory.HELVETICA, 9, BaseColor.RED);

    // -------- HEADER TABLE (3 Columns for Logo - Title - Logo) --------
    PdfPTable headerTbl = new PdfPTable(3);
    headerTbl.WidthPercentage = 100;
    headerTbl.SetWidths(new float[] { 20f, 60f, 20f });

    // Left Logo
    iTextSharp.text.Image leftLogo = iTextSharp.text.Image.GetInstance(@"C:\Cybersoft_Doc\Images\logo1.jpg");
    leftLogo.ScaleAbsolute(90, 35);
    PdfPCell leftLogoCell = new PdfPCell(leftLogo);
    leftLogoCell.Border = Rectangle.NO_BORDER;
    leftLogoCell.HorizontalAlignment = Element.ALIGN_LEFT;

    // Title
    PdfPCell titleCell = new PdfPCell(new Phrase("Permit for Accommodation at Holiday Home", titleFont));
    titleCell.HorizontalAlignment = Element.ALIGN_CENTER;
    titleCell.Border = Rectangle.NO_BORDER;
    titleCell.PaddingTop = 10;

    // Right Logo
    iTextSharp.text.Image rightLogo = iTextSharp.text.Image.GetInstance(@"C:\Cybersoft_Doc\Images\logo2.jpg");
    rightLogo.ScaleAbsolute(60, 55);
    PdfPCell rightLogoCell = new PdfPCell(rightLogo);
    rightLogoCell.Border = Rectangle.NO_BORDER;
    rightLogoCell.HorizontalAlignment = Element.ALIGN_RIGHT;

    headerTbl.AddCell(leftLogoCell);
    headerTbl.AddCell(titleCell);
    headerTbl.AddCell(rightLogoCell);

    doc.Add(headerTbl);
    doc.Add(new Paragraph("\n"));

    // -------- RECEIPT NO --------
    Paragraph rec = new Paragraph($"Receipt No.: {receiptNo}", boldFont);
    rec.Alignment = Element.ALIGN_CENTER;
    doc.Add(rec);
    doc.Add(new Paragraph("\n"));

    // ---------- EMPLOYEE DETAILS TABLE (2 Column layout exactly like your reference) ----------
    PdfPTable empTbl = new PdfPTable(4);
    empTbl.WidthPercentage = 100;
    empTbl.SetWidths(new float[] { 18f, 32f, 18f, 32f });

    void AddRow(string label1, string val1, string label2, string val2)
    {
        empTbl.AddCell(new PdfPCell(new Phrase(label1, boldFont)) { Border = Rectangle.NO_BORDER });
        empTbl.AddCell(new PdfPCell(new Phrase(val1, subFont)) { Border = Rectangle.NO_BORDER });

        empTbl.AddCell(new PdfPCell(new Phrase(label2, boldFont)) { Border = Rectangle.NO_BORDER });
        empTbl.AddCell(new PdfPCell(new Phrase(val2, subFont)) { Border = Rectangle.NO_BORDER });
    }

    AddRow("Name:", empName, "Personal No.:", perno);
    AddRow("Department:", "Information Technology", "Mobile No.:", "-");
    AddRow("Location:", location, "Guest House:", hotel);
    AddRow("Check-In Date:", fromdt, "Check-Out Date:", Todt);
    AddRow("Check-In Time:", "09:00 AM", "Check-Out Time:", "07:00 AM");
    AddRow("Duration:", "3", "", "");

    doc.Add(empTbl);
    doc.Add(new Paragraph("\n"));

    // ---------- ROOM DETAILS ----------
    Paragraph roomTitle = new Paragraph("ROOM DETAILS", boldFont);
    roomTitle.Alignment = Element.ALIGN_LEFT;
    doc.Add(roomTitle);
    doc.Add(new Paragraph("\n"));

    PdfPTable roomTbl = new PdfPTable(3);
    roomTbl.WidthPercentage = 100;
    roomTbl.SetWidths(new float[] { 30f, 35f, 35f });

    // Header
    string[] roomHeaders = { "Date", "Choice 1", "Choice 2" };
    foreach (var h in roomHeaders)
    {
        PdfPCell c = new PdfPCell(new Phrase(h, boldFont));
        c.BackgroundColor = new BaseColor(240, 240, 240);
        c.HorizontalAlignment = Element.ALIGN_CENTER;
        roomTbl.AddCell(c);
    }

    DataTable dtRooms = GetRoomDetails(perno, receiptNo);
    foreach (DataRow r in dtRooms.Rows)
    {
        roomTbl.AddCell(new PdfPCell(new Phrase(r["BEGDA"].ToString(), subFont)));
        roomTbl.AddCell(new PdfPCell(new Phrase(r["ROOM_NO1"].ToString(), subFont)));
        roomTbl.AddCell(new PdfPCell(new Phrase(r["ROOM_NO2"].ToString(), subFont)));
    }

    doc.Add(roomTbl);
    doc.Add(new Paragraph("\n"));

    // ---------- FAMILY DETAILS ----------
    Paragraph famTitle = new Paragraph("FAMILY DETAILS", boldFont);
    doc.Add(famTitle);
    doc.Add(new Paragraph("\n"));

    PdfPTable famTbl = new PdfPTable(4);
    famTbl.WidthPercentage = 100;
    famTbl.SetWidths(new float[] { 40f, 20f, 20f, 20f });

    string[] famHeaders = { "Name", "Relationship", "Age", "Gender" };
    foreach (var h in famHeaders)
    {
        PdfPCell c = new PdfPCell(new Phrase(h, boldFont));
        c.BackgroundColor = new BaseColor(240, 240, 240);
        c.HorizontalAlignment = Element.ALIGN_CENTER;
        famTbl.AddCell(c);
    }

    DataTable dtFamily = GetFamilyDetails(perno, receiptNo);
    foreach (DataRow f in dtFamily.Rows)
    {
        famTbl.AddCell(new PdfPCell(new Phrase(f["NAME"].ToString(), subFont)));
        famTbl.AddCell(new PdfPCell(new Phrase(f["RELATION_CODE"].ToString(), subFont)));
        famTbl.AddCell(new PdfPCell(new Phrase(f["AGE"].ToString(), subFont)));
        famTbl.AddCell(new PdfPCell(new Phrase(f["GENDER_CODE"].ToString(), subFont)));
    }

    doc.Add(famTbl);
    doc.Add(new Paragraph("\n"));

    // -------- TERMS AND CONDITIONS (Red text + bold heading) --------
    Paragraph tcTitle = new Paragraph("Terms And Conditions\n\n", boldFont);
    tcTitle.Alignment = Element.ALIGN_CENTER;
    doc.Add(tcTitle);

    string tc = @"* The employee in whose name the Holiday Home is booked must carry ID proof.
* Occupancy is to be maintained strictly.
* Use of alcohol is strictly prohibited.

* Employees are advised to cancel booking 15 days prior to occupancy date,
  else charges will be deducted.

* Late checkout retention charges may apply.
* Damage to hotel property must be settled before checkout.";

    Paragraph tcPara = new Paragraph(tc, redFont);
    doc.Add(tcPara);

    doc.Close();
    return pdfPath;
}



 
 
 
 public string GenerateBookingPDF(string receiptNo, string perno, string fromdt, string Todt,
                                 string location, string hotel, string empName)
        {
            string folderpath = @"C:\Cybersoft_Doc\SCH";

            string pdfPath = Path.Combine(folderpath,$"permit.pdf");

            Document doc = new Document(PageSize.A4, 40, 40, 40, 40);
            PdfWriter.GetInstance(doc, new FileStream(pdfPath, FileMode.Create));
            doc.Open();

            // ---------- HEADER ----------
            var titleFont = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 16);
            var subFont = FontFactory.GetFont(FontFactory.HELVETICA, 12);

            Paragraph header = new Paragraph("Permit for Accommodation at Holiday Home\n\n", titleFont);
            header.Alignment = Element.ALIGN_CENTER;
            doc.Add(header);

            doc.Add(new Paragraph($"Receipt No: {receiptNo}", subFont));
            doc.Add(new Paragraph($"Employee No: {perno}", subFont));
            doc.Add(new Paragraph($"Employee Name: {empName}", subFont));
            doc.Add(new Paragraph($"Location: {location}", subFont));
            doc.Add(new Paragraph($"Guest House: {hotel}", subFont));
            doc.Add(new Paragraph($"Check-In: {fromdt}", subFont));
            doc.Add(new Paragraph($"Check-Out: {Todt}\n\n", subFont));


            // ---------- ROOM DETAILS TABLE ----------
            PdfPTable roomTbl = new PdfPTable(3);
            roomTbl.WidthPercentage = 100;

            roomTbl.AddCell("Date");
            roomTbl.AddCell("Room No. 1");
            roomTbl.AddCell("Room No. 2");

            DataTable dtRooms = GetRoomDetails(perno, receiptNo);
            foreach (DataRow r in dtRooms.Rows)
            {
                roomTbl.AddCell(r["BEGDA"].ToString());
                roomTbl.AddCell(r["ROOM_NO1"].ToString());
                roomTbl.AddCell(r["ROOM_NO2"].ToString());
            }

            doc.Add(new Paragraph("ROOM DETAILS\n\n", titleFont));
            doc.Add(roomTbl);

            doc.Add(new Paragraph("\n"));

            // ---------- FAMILY DETAILS ----------
            PdfPTable famTbl = new PdfPTable(4);
            famTbl.WidthPercentage = 100;

            famTbl.AddCell("Name");
            famTbl.AddCell("Relation");
            famTbl.AddCell("Age");
            famTbl.AddCell("Gender");

            DataTable dtFamily = GetFamilyDetails(perno, receiptNo);
            foreach (DataRow f in dtFamily.Rows)
            {
                famTbl.AddCell(f["NAME"].ToString());
                famTbl.AddCell(f["RELATION_CODE"].ToString());
                famTbl.AddCell(f["AGE"].ToString());
                famTbl.AddCell(f["GENDER_CODE"].ToString());
            }

            doc.Add(new Paragraph("FAMILY DETAILS\n\n", titleFont));
            doc.Add(famTbl);

            doc.Add(new Paragraph("\n"));

            // ---------- TERMS & CONDITIONS ----------
            doc.Add(new Paragraph("Terms & Conditions", titleFont));
            doc.Add(new Paragraph(
                "* The employee must carry company ID and permit.\n" +
                "* Occupancy is strictly as per allotted rooms.\n" +
                "* Use of alcohol is prohibited.\n" +
                "* Cancellation rules apply.\n" +
                "* Late checkout may incur retention charges.\n" +
                "* Damage to property must be compensated.\n"
            ));

            doc.Close();

            return pdfPath;
        }
