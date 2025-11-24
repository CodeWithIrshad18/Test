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



 
 
 
 public string GenerateBookingPDF(string receiptNo, string perno, string fromdt, string Todt,
            string location, string hotel, string empName)
        {
            string folderpath = @"C:\Cybersoft_Doc\SCH";
            string pdfPath = Path.Combine(folderpath, $"Permit.pdf");

            Document doc = new Document(PageSize.A4, 40, 40, 40, 40);
            PdfWriter.GetInstance(doc, new FileStream(pdfPath, FileMode.Create));
            doc.Open();

            // ---------------- HEADER LOGOS ----------------
            PdfPTable headerTbl = new PdfPTable(3);
            headerTbl.WidthPercentage = 100;
            float[] headerWidths = { 25, 50, 25 };
            headerTbl.SetWidths(headerWidths);

            iTextSharp.text.Image logo2 = iTextSharp.text.Image.GetInstance(@"C:\Cybersoft_Doc\SCH\Images\logo2.png");
            logo2.ScaleAbsolute(70, 45);

            iTextSharp.text.Image logo1 = iTextSharp.text.Image.GetInstance(@"C:\Cybersoft_Doc\SCH\Images\logo1.jpg");
            logo1.ScaleAbsolute(100, 80);



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

            DataTable empDt = GetEmployeeDetails(perno);

            string department = "";
            string phone = "";

            if (empDt.Rows.Count > 0)
            {
                department = empDt.Rows[0]["Department"].ToString();
                phone = empDt.Rows[0]["Phone"].ToString();
            }

            string guestHouseCode = "";

            DataTable dtHeader2 = GetHeaderDetails(perno, receiptNo);

            if (dtHeader2.Rows.Count > 0)
            {
                guestHouseCode = dtHeader2.Rows[0]["GSTHOUSE_ID"].ToString();
            }

            DataTable dtTime = GetCheckInOutTime(guestHouseCode);

            string chkInTime = "";
            string chkOutTime = "";

            if (dtTime.Rows.Count > 0)
            {
                chkInTime = dtTime.Rows[0]["CheckIn"].ToString();
                chkOutTime = dtTime.Rows[0]["CheckOut"].ToString();
            }


            // ---------------- BASIC DETAILS ----------------
            Font normal = FontFactory.GetFont(FontFactory.HELVETICA, 10);
            Font bold = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10);

            PdfPTable detailTbl = new PdfPTable(2);
            detailTbl.WidthPercentage = 100;
            float[] detailWidths = { 50, 50 };
            detailTbl.SetWidths(detailWidths);

            detailTbl.AddCell(CreateCell($"Name: {empName}", normal));
            detailTbl.AddCell(CreateCell($"Personal No.: {perno}", normal));

            detailTbl.AddCell(CreateCell($"Department: {department}", normal));
            detailTbl.AddCell(CreateCell($"Phone: {phone}", normal));

            detailTbl.AddCell(CreateCell($"Location: {location}", normal));
            detailTbl.AddCell(CreateCell($"Guest House: {hotel}", normal));

            detailTbl.AddCell(CreateCell($"Check-In Date: {fromdt}", normal));
            detailTbl.AddCell(CreateCell($"Check-Out Date: {Todt}", normal));

            detailTbl.AddCell(CreateCell($"Check-In Time: {chkInTime}", normal));
            detailTbl.AddCell(CreateCell($"Check-Out Time: {chkOutTime}", normal));

            doc.Add(detailTbl);

            doc.Add(new Paragraph("\nROOM DETAILS\n", bold));

            // ---------------- ROOM DETAILS ----------------
            PdfPTable roomTbl = new PdfPTable(3);
            roomTbl.WidthPercentage = 100;
            roomTbl.AddCell(Header("Date"));
            roomTbl.AddCell(Header("Choice 1"));
            roomTbl.AddCell(Header("Choice 2"));

            DataTable dtHeader = GetHeaderDetails(perno, receiptNo);

            foreach (DataRow r in dtHeader.Rows)
            {
                string guestHouseId = r["GSTHOUSE_ID"].ToString();
                string locId = r["GSTHOUSE_LOC_ID"].ToString();

                string roomNo1 = r["ROOM_NO1"].ToString();
                string roomNo2 = r["ROOM_NO2"].ToString();

                string roomType1 = GetRoomTypeFromOracle(guestHouseId, locId, roomNo1);
                string roomType2 = GetRoomTypeFromOracle(guestHouseId, locId, roomNo2);

                string desc1 = GetRoomTypeDescription(roomType1);
                string desc2 = GetRoomTypeDescription(roomType2);

                string displayRoom1 = roomNo1 + " - " + desc1;
                string displayRoom2 = roomNo2 + " - " + desc2;

                roomTbl.AddCell(Convert.ToDateTime(r["BEGDA"]).ToString("dd-MM-yyyy"));
                roomTbl.AddCell(displayRoom1);
                roomTbl.AddCell(displayRoom2);
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

            string amtDeduct = "0";

            if (dtHeader.Rows.Count > 0)
            {
                amtDeduct = dtHeader.Rows[0]["AMT_DEDUCT"] == DBNull.Value
                            ? "0"
                            : dtHeader.Rows[0]["AMT_DEDUCT"].ToString();
            }

            // ---------------- MEMBER DETAILS ----------------
            doc.Add(new Paragraph("\nMEMBER DETAILS\n", bold));

         

            PdfPTable memTbl = new PdfPTable(4);
            memTbl.WidthPercentage = 100;

            memTbl.AddCell(Header($"Child: {childCount}"));
            memTbl.AddCell(Header($"Adult: {adultCount}"));
            memTbl.AddCell(Header($"Total No. of persons: {dtFamily.Rows.Count}"));
            memTbl.AddCell(Header($"Total Charges: ₹ {amtDeduct}"));


            doc.Add(memTbl);
            doc.Add(new Paragraph("\n"));



            string contactPerson = GetContactPerson(guestHouseCode);

            // ---------------- WITH REGARDS BLOCK ----------------
            PdfPTable regardTbl = new PdfPTable(2);
            regardTbl.WidthPercentage = 100;
            regardTbl.SetWidths(new float[] { 50, 50 });

            regardTbl.AddCell(new PdfPCell(new Phrase("With Regards,\nArea Manager, Employment Bureau\n(Group HR/IR)", normal))
            {
                Border = 0,
                HorizontalAlignment = Element.ALIGN_LEFT
            });

            regardTbl.AddCell(new PdfPCell(new Phrase(
    $"Contact Person for Holiday Home\n{contactPerson}", bold))
            {
                Border = 0,
                HorizontalAlignment = Element.ALIGN_RIGHT
            });

            doc.Add(regardTbl);

            // ---------------- TERMS & CONDITIONS ----------------
            doc.Add(new Paragraph("\n*** SINCE IT IS AN ELECTRONICALLY GENERATED PERMIT SO SIGNATURE IS NOT REQUIRED ***\n",bold));

            doc.Add(new Paragraph("\nTerms And Conditions\n", bold));

            doc.Add(new Paragraph("\nThe employee in whose name the Holiday Home is booked must carry an identity proof(i.e Company's\r\n Gate-pass and Govt. ID Card for all family members) along with the permit and the same should be\r\n produced at the time of occupation of Holiday Home.\n", normal));

            doc.Add(new Paragraph("\nOccupancy is to be maintained strictly as per the permit issued to the employee concerned.\n", normal));

            doc.Add(new Paragraph("\nUse of alcohol in the premises is strictly prohibited.\n", normal));

            doc.Close();
            return pdfPath;
        }
