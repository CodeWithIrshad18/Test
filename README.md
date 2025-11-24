public string GenerateBookingPDF(string receiptNo, string perno, string fromdt, string Todt,
     string location, string hotel, string empName)
{
    string folderpath = @"C:\Cybersoft_Doc\SCH";
    string pdfPath = Path.Combine(folderpath, $"Permit.pdf");

    Document doc = new Document(PageSize.A4, 35, 35, 35, 45);
    PdfWriter writer = PdfWriter.GetInstance(doc, new FileStream(pdfPath, FileMode.Create));
    doc.Open();

    // ---------------- Fonts ----------------
    Font font10 = FontFactory.GetFont(FontFactory.HELVETICA, 10);
    Font bold10 = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10);
    Font bold11 = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 11);
    Font titleFont = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 14);
    Font footerRed = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 9, BaseColor.RED);

    // ---------------- HEADER ----------------
    PdfPTable header = new PdfPTable(3);
    header.WidthPercentage = 100;
    header.SetWidths(new float[] { 25, 50, 25 });

    Image leftLogo = Image.GetInstance(@"C:\Cybersoft_Doc\SCH\Images\logo1.jpg");
    leftLogo.ScaleAbsolute(90, 70);

    Image rightLogo = Image.GetInstance(@"C:\Cybersoft_Doc\SCH\Images\logo2.png");
    rightLogo.ScaleAbsolute(70, 45);

    header.AddCell(new PdfPCell(leftLogo) { Border = 0, HorizontalAlignment = Element.ALIGN_LEFT });
    header.AddCell(new PdfPCell(new Phrase("Permit for Accomodation at Holiday Home", titleFont))
    {
        Border = 0,
        HorizontalAlignment = Element.ALIGN_CENTER,
        VerticalAlignment = Element.ALIGN_MIDDLE
    });

    header.AddCell(new PdfPCell(rightLogo) { Border = 0, HorizontalAlignment = Element.ALIGN_RIGHT });

    doc.Add(header);

    Paragraph rec = new Paragraph($"Receipt No.: {receiptNo}", bold10);
    rec.Alignment = Element.ALIGN_CENTER;
    rec.SpacingAfter = 10;
    doc.Add(rec);

    // ---------------- EMPLOYEE DETAILS ----------------
    DataTable emp = GetEmployeeDetails(perno);
    string dept = emp.Rows.Count > 0 ? emp.Rows[0]["Department"].ToString() : "";
    string phone = emp.Rows.Count > 0 ? emp.Rows[0]["Phone"].ToString() : "";

    DataTable booking = GetHeaderDetails(perno, receiptNo);
    string ghCode = booking.Rows[0]["GSTHOUSE_ID"].ToString();
    DataTable time = GetCheckInOutTime(ghCode);

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

    doc.Add(detail);

    // ---------------- ROOM DETAILS ----------------
    Section(doc, "ROOM DETAILS");

    PdfPTable room = new PdfPTable(3) { WidthPercentage = 100 };
    room.AddCell(Header("Date"));
    room.AddCell(Header("Choice 1"));
    room.AddCell(Header("Choice 2"));

    foreach (DataRow r in booking.Rows)
    {
        room.AddCell(FormatDate(r["BEGDA"].ToString()));
        room.AddCell(r["ROOM_NO1"].ToString());
        room.AddCell(r["ROOM_NO2"].ToString());
    }

    doc.Add(room);

    // ---------------- FAMILY DETAILS ----------------
    Section(doc, "FAMILY DETAILS");

    PdfPTable fam = new PdfPTable(4) { WidthPercentage = 100 };
    fam.AddCell(Header("Name"));
    fam.AddCell(Header("Relationship"));
    fam.AddCell(Header("Age"));
    fam.AddCell(Header("Gender"));

    DataTable family = GetFamilyDetails(perno, receiptNo);
    int child = 0, adult = 0;

    foreach (DataRow f in family.Rows)
    {
        int age = Convert.ToInt32(f["AGE"]);
        if (age < 18) child++; else adult++;

        fam.AddCell(f["NAME"].ToString());
        fam.AddCell(MapRelation(f["RELATION_CODE"].ToString()));
        fam.AddCell(age.ToString());
        fam.AddCell(MapGender(f["GENDER_CODE"].ToString()));
    }

    doc.Add(fam);

    // ---------------- MEMBER DETAILS ----------------
    Section(doc, "MEMBER DETAILS");

    PdfPTable mem = new PdfPTable(2) { WidthPercentage = 100 };
    string amt = booking.Rows[0]["AMT_DEDUCT"].ToString();

    mem.AddCell(Value($"Child: {child}"));
    mem.AddCell(Value($"Adult: {adult}"));
    mem.AddCell(Value($"Total No. of persons: {family.Rows.Count}"));
    mem.AddCell(Value($"Total Charges: Rs.{amt}"));

    doc.Add(mem);

    // ---------------- FOOTER CONTACT ----------------
    string contact = GetContactPerson(ghCode);

    PdfPTable sign = new PdfPTable(2)
    {
        WidthPercentage = 100,
        SpacingBefore = 10
    };

    sign.AddCell(new PdfPCell(new Phrase("With Regards,\nArea Manager, Employment Bureau\n(Group HR/IR)", font10)) { Border = 0 });

    sign.AddCell(new PdfPCell(new Phrase($"Contact Person for Holiday Home\n{contact}", bold10))
    {
        Border = 0,
        HorizontalAlignment = Element.ALIGN_RIGHT
    });

    doc.Add(sign);

    // ---------------- NOTICE & TERMS SECTION ----------------
    doc.Add(new Paragraph("\n*** SINCE IT IS AN ELECTRONICALLY GENERATED PERMIT SO SIGNATURE IS NOT REQUIRED ***", bold10));

    Paragraph terms = new Paragraph("\nTerms And Conditions", bold11);
    terms.Alignment = Element.ALIGN_CENTER;
    terms.SpacingAfter = 5;
    doc.Add(terms);

    string[] policyLines =
    {
        "* The employee must carry ID proof along with this permit.\n",
        "* Occupancy must be maintained strictly as mentioned.\n",
        "* Use of alcohol is strictly prohibited.\n",
        "* Cancellation policy: Minimum 15 days prior notice required.\n",
        "* Late checkout may result in penalty.\n",
        "* Any property damage will be recovered from employee salary.\n"
    };

    foreach (string line in policyLines)
        doc.Add(new Paragraph(line, footerRed));

    doc.Close();
    return pdfPath;
}

// ----------------------------------------------------------
// Helper Methods
// ----------------------------------------------------------
private PdfPCell Label(string t) => new PdfPCell(new Phrase(t + " :", FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10))) { Border = 0 };
private PdfPCell Value(string t) => new PdfPCell(new Phrase(t, FontFactory.GetFont(FontFactory.HELVETICA, 10))) { Border = 0 };
private string FormatDate(string d) => Convert.ToDateTime(d).ToString("dd.MM.yyyy");
private PdfPCell Header(string t) => new PdfPCell(new Phrase(t, FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10)))
{
    BackgroundColor = BaseColor.LIGHT_GRAY,
    Padding = 5,
    HorizontalAlignment = Element.ALIGN_CENTER
};

private void Section(Document doc, string title)
{
    PdfPTable table = new PdfPTable(1) { WidthPercentage = 100, SpacingBefore = 12, SpacingAfter = 3 };
    table.AddCell(new PdfPCell(new Phrase(title, FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 11)))
    {
        BackgroundColor = BaseColor.LIGHT_GRAY,
        Padding = 5
    });
    doc.Add(table);
}


        
        
        
        public string GenerateBookingPDF(string receiptNo, string perno, string fromdt, string Todt,
     string location, string hotel, string empName)
        {
            string folderpath = @"C:\Cybersoft_Doc\SCH";
            string pdfPath = Path.Combine(folderpath, $"Permit.pdf");

            Document doc = new Document(PageSize.A4, 40, 40, 40, 40);
            PdfWriter.GetInstance(doc, new FileStream(pdfPath, FileMode.Create));
            doc.Open();

            Font normal = FontFactory.GetFont(FontFactory.HELVETICA, 10);
            Font bold = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10);
            Font titleFont = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 14);

            // ================= HEADER =================

            PdfPTable headerTbl = new PdfPTable(3);
            headerTbl.WidthPercentage = 100;
            headerTbl.SetWidths(new float[] { 25, 50, 25 });

            Image logoLeft = Image.GetInstance(@"C:\Cybersoft_Doc\SCH\Images\logo1.jpg");
            logoLeft.ScaleAbsolute(90, 70);

            Image logoRight = Image.GetInstance(@"C:\Cybersoft_Doc\SCH\Images\logo2.png");
            logoRight.ScaleAbsolute(70, 45);

            headerTbl.AddCell(new PdfPCell(logoLeft) { Border = 0, HorizontalAlignment = Element.ALIGN_LEFT });
            headerTbl.AddCell(new PdfPCell(new Phrase("Permit for Accommodation at Holiday Home", titleFont))
            {
                Border = 0,
                HorizontalAlignment = Element.ALIGN_CENTER,
                VerticalAlignment = Element.ALIGN_MIDDLE
            });
            headerTbl.AddCell(new PdfPCell(logoRight) { Border = 0, HorizontalAlignment = Element.ALIGN_RIGHT });

            doc.Add(headerTbl);

            Paragraph receiptPara = new Paragraph($"Receipt No.: {receiptNo}", bold);
            receiptPara.Alignment = Element.ALIGN_CENTER;
            receiptPara.SpacingAfter = 10f;
            doc.Add(receiptPara);

            // ================= EMPLOYEE DATA =================

            DataTable empDt = GetEmployeeDetails(perno);
            string department = "", phone = "";

            if (empDt.Rows.Count > 0)
            {
                department = empDt.Rows[0]["Department"].ToString();
                phone = empDt.Rows[0]["Phone"].ToString();
            }

            DataTable dtHeader = GetHeaderDetails(perno, receiptNo);
            string guestHouseCode = dtHeader.Rows[0]["GSTHOUSE_ID"].ToString();

            DataTable dtTime = GetCheckInOutTime(guestHouseCode);
            string chkInTime = dtTime.Rows[0]["CheckIn"].ToString();
            string chkOutTime = dtTime.Rows[0]["CheckOut"].ToString();

            PdfPTable detailTbl = new PdfPTable(4);
            detailTbl.WidthPercentage = 100;
            detailTbl.SetWidths(new float[] { 18, 32, 18, 32 });

            detailTbl.AddCell(LabelCell("Name")); detailTbl.AddCell(ValueCell(empName));
            detailTbl.AddCell(LabelCell("Personal No.")); detailTbl.AddCell(ValueCell(perno));

            detailTbl.AddCell(LabelCell("Department")); detailTbl.AddCell(ValueCell(department));
            detailTbl.AddCell(LabelCell("Mobile No.")); detailTbl.AddCell(ValueCell(phone));

            detailTbl.AddCell(LabelCell("Location")); detailTbl.AddCell(ValueCell(location));
            detailTbl.AddCell(LabelCell("Guest House")); detailTbl.AddCell(ValueCell(hotel));

            detailTbl.AddCell(LabelCell("CheckIn Date")); detailTbl.AddCell(ValueCell(fromdt));
            detailTbl.AddCell(LabelCell("CheckOut Date")); detailTbl.AddCell(ValueCell(Todt));

            detailTbl.AddCell(LabelCell("CheckIn Time")); detailTbl.AddCell(ValueCell(chkInTime));
            detailTbl.AddCell(LabelCell("CheckOut Time")); detailTbl.AddCell(ValueCell(chkOutTime));

            doc.Add(detailTbl);

            // ================= ROOM DETAILS =================

            SectionTitle(doc, "ROOM DETAILS");

            PdfPTable roomTbl = new PdfPTable(3) { WidthPercentage = 100 };
            roomTbl.AddCell(Header("Date"));
            roomTbl.AddCell(Header("Choice 1"));
            roomTbl.AddCell(Header("Choice 2"));

            foreach (DataRow r in dtHeader.Rows)
            {
                string displayRoom1 = r["ROOM_NO1"] + " - " + GetRoomTypeDescription(
                GetRoomTypeFromOracle(r["GSTHOUSE_ID"].ToString(), r["GSTHOUSE_LOC_ID"].ToString(), r["ROOM_NO1"].ToString()));

                string displayRoom2 = r["ROOM_NO2"] + " - " + GetRoomTypeDescription(
                GetRoomTypeFromOracle(r["GSTHOUSE_ID"].ToString(), r["GSTHOUSE_LOC_ID"].ToString(), r["ROOM_NO2"].ToString()));

                roomTbl.AddCell(Convert.ToDateTime(r["BEGDA"]).ToString("dd.MM.yyyy"));
                roomTbl.AddCell(displayRoom1);
                roomTbl.AddCell(displayRoom2);
            }
            doc.Add(roomTbl);

            // ================= FAMILY DETAILS =================

            SectionTitle(doc, "FAMILY DETAILS");

            PdfPTable famTbl = new PdfPTable(4) { WidthPercentage = 100 };
            famTbl.AddCell(Header("Name"));
            famTbl.AddCell(Header("Relationship"));
            famTbl.AddCell(Header("Age"));
            famTbl.AddCell(Header("Gender"));

            DataTable dtFamily = GetFamilyDetails(perno, receiptNo);

            int child = 0, adult = 0;

            foreach (DataRow f in dtFamily.Rows)
            {
                int age = Convert.ToInt32(f["AGE"]);
                if (age < 18) child++; else adult++;

                famTbl.AddCell(f["NAME"].ToString());
                famTbl.AddCell(MapRelation(f["RELATION_CODE"].ToString()));
                famTbl.AddCell(age.ToString());
                famTbl.AddCell(MapGender(f["GENDER_CODE"].ToString()));
            }
            doc.Add(famTbl);

            // ================= MEMBER DETAILS =================

            SectionTitle(doc, "MEMBER DETAILS");

            string amt = dtHeader.Rows[0]["AMT_DEDUCT"]?.ToString() ?? "0";

            PdfPTable memTbl = new PdfPTable(2) { WidthPercentage = 100 };
            memTbl.AddCell(ValueCell($"Child : {child}"));
            memTbl.AddCell(ValueCell($"Adult : {adult}"));
            memTbl.AddCell(ValueCell($"Total No. of persons : {dtFamily.Rows.Count}"));
            memTbl.AddCell(ValueCell($"Total Charges : Rs.{amt}"));

            doc.Add(memTbl);

            string contact = GetContactPerson(guestHouseCode);

            PdfPTable signTbl = new PdfPTable(2) { WidthPercentage = 100 };
            signTbl.AddCell(new PdfPCell(new Phrase("With Regards,\nArea Manager, Employment Bureau\n(Group HR/IR)", normal)) { Border = 0 });
            signTbl.AddCell(new PdfPCell(new Phrase($"Contact Person for Holiday Home\n{contact}", bold))
            {
                Border = 0,
                HorizontalAlignment = Element.ALIGN_RIGHT
            });
            doc.Add(signTbl);

            doc.Add(new Paragraph("\n*** SINCE IT IS AN ELECTRONICALLY GENERATED PERMIT SO SIGNATURE IS NOT REQUIRED ***", bold));
            doc.Add(new Paragraph("\nTerms And Conditions", bold));

            Font warning = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 9, BaseColor.RED);

            doc.Add(new Paragraph("* The employee in whose name the Holiday Home is booked must carry an identity proof along with the permit and same should be produced at the time of occupation.\n", normal));
            doc.Add(new Paragraph("* Occupancy is to be maintained strictly as per the permit issued.\n", normal));
            doc.Add(new Paragraph("* Use of alcohol in the premises is strictly prohibited.\n", normal));
            doc.Add(new Paragraph("* Employees are advised to cancel the booking atleast 15 days prior to start of occupancy...", warning));

            doc.Close();
            return pdfPath;
        }

        // ---------------- HELPER METHODS ----------------


        private PdfPCell LabelCell(string text)
        {
            return new PdfPCell(new Phrase(text + " :", FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10)))
            { Border = 0 };
        }

        private PdfPCell ValueCell(string text)
        {
            return new PdfPCell(new Phrase(text, FontFactory.GetFont(FontFactory.HELVETICA, 10)))
            { Border = 0 };
        }

        private void SectionTitle(Document doc, string title)
        {
            PdfPTable tbl = new PdfPTable(1) { WidthPercentage = 100 };
            tbl.AddCell(new PdfPCell(new Phrase(title, FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 11)))
            {
                BackgroundColor = BaseColor.LIGHT_GRAY,
                Padding = 5
            });
            doc.Add(tbl);
        }

        private PdfPCell Header(string text)
        {
            return new PdfPCell(new Phrase(text, FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10)))
            {
                HorizontalAlignment = Element.ALIGN_CENTER,
                BackgroundColor = BaseColor.LIGHT_GRAY,
                Padding = 5
            };
        }
