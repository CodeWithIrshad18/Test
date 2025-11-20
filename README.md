public string GenerateBookingPDF(string receiptNo, string perno, string fromdt, string Todt,
                                  string location, string hotel, string empName, 
                                  string Gcode, string RoomType, string mobileNo)
{
    string folderpath = @"C:\Cybersoft_Doc\SCH";
    if (!Directory.Exists(folderpath))
        Directory.CreateDirectory(folderpath);

    string pdfPath = Path.Combine(folderpath, $"Permit.pdf");

    Document doc = new Document(PageSize.A4, 40, 40, 40, 40);
    PdfWriter.GetInstance(doc, new FileStream(pdfPath, FileMode.Create));
    doc.Open();

    // ------------------------------------------------------------------
    // FONTS
    // ------------------------------------------------------------------
    Font titleFont = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 14);
    Font bold = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10);
    Font normal = FontFactory.GetFont(FontFactory.HELVETICA, 10);

    // ------------------------------------------------------------------
    // QUERY DATA
    // ------------------------------------------------------------------

    // 1. Employee Department & Phone
    DataSet dsEmp = GetData(perno);
    string department = dsEmp.Tables[0].Rows[0]["Department"].ToString();
    string phone = dsEmp.Tables[0].Rows[0]["Phone"].ToString();

    // 2. CheckIn / CheckOut Time
    DataSet dsTime = Get_ChkInChkOutTime(Gcode);
    string checkInTime = dsTime.Tables[0].Rows[0]["checkIn"].ToString();
    string checkOutTime = dsTime.Tables[0].Rows[0]["checkOut"].ToString();

    // 3. Room Type Description
    DataSet dsRoomType = Get_RoomTyp_Descrptn(RoomType);
    string roomTypeDescr = dsRoomType.Tables[0].Rows[0]["RoomTypeDescription"].ToString();

    // 4. Room Details
    DataTable dtRooms = GetRoomDetails(perno, receiptNo);

    // 5. Family Details
    DataTable dtFamily = GetFamilyDetails(perno, receiptNo);

    int childCount = dtFamily.AsEnumerable().Count(r => Convert.ToInt32(r["AGE"]) < 18);
    int adultCount = dtFamily.AsEnumerable().Count(r => Convert.ToInt32(r["AGE"]) >= 18);

    // 6. Total Charges query
    string totalCharges = GetTotalCharges(receiptNo);   // <--- YOUR QUERY METHOD

    // Duration (days)
    int duration = (Convert.ToDateTime(Todt) - Convert.ToDateTime(fromdt)).Days;

    // ------------------------------------------------------------------
    // HEADER WITH LOGOS
    // ------------------------------------------------------------------
    PdfPTable headerTbl = new PdfPTable(3);
    headerTbl.WidthPercentage = 100;
    headerTbl.SetWidths(new float[] { 25, 50, 25 });

    iTextSharp.text.Image logo1 = iTextSharp.text.Image.GetInstance(@"C:\Cybersoft_Doc\Images\logo1.jpg");
    logo1.ScaleAbsolute(110, 45);

    iTextSharp.text.Image logo2 = iTextSharp.text.Image.GetInstance(@"C:\Cybersoft_Doc\Images\logo2.jpg");
    logo2.ScaleAbsolute(100, 55);

    headerTbl.AddCell(new PdfPCell(logo1) { Border = 0, HorizontalAlignment = Element.ALIGN_LEFT });
    headerTbl.AddCell(new PdfPCell(new Phrase("Permit for Accomodation at Holiday Home", titleFont))
    {
        Border = 0,
        HorizontalAlignment = Element.ALIGN_CENTER,
        VerticalAlignment = Element.ALIGN_MIDDLE
    });
    headerTbl.AddCell(new PdfPCell(logo2) { Border = 0, HorizontalAlignment = Element.ALIGN_RIGHT });

    doc.Add(headerTbl);
    doc.Add(new Paragraph("\n"));

    // ------------------------------------------------------------------
    // RECEIPT NO
    // ------------------------------------------------------------------
    doc.Add(new Paragraph($"Receipt No.: {receiptNo}", bold));
    doc.Add(new Paragraph("\n"));

    // ------------------------------------------------------------------
    // EMPLOYEE DETAILS (2 column layout)
    // ------------------------------------------------------------------
    PdfPTable empTbl = new PdfPTable(2);
    empTbl.WidthPercentage = 100;
    empTbl.SetWidths(new float[] { 50, 50 });

    empTbl.AddCell(Cell($"Name: {empName}", normal));
    empTbl.AddCell(Cell($"Personal No.: {perno}", normal));

    empTbl.AddCell(Cell($"Department: {department}", normal));
    empTbl.AddCell(Cell($"Mobile No.: {mobileNo}", normal));

    empTbl.AddCell(Cell($"Location: {location}", normal));
    empTbl.AddCell(Cell($"Guest House: {hotel}", normal));

    empTbl.AddCell(Cell($"CheckIn Date: {fromdt}", normal));
    empTbl.AddCell(Cell($"CheckOut Date: {Todt}", normal));

    empTbl.AddCell(Cell($"CheckIn Time: {checkInTime}", normal));
    empTbl.AddCell(Cell($"CheckOut Time: {checkOutTime}", normal));

    empTbl.AddCell(Cell($"Duration: {duration}", normal));
    empTbl.AddCell(Cell("", normal));

    doc.Add(empTbl);

    // ------------------------------------------------------------------
    // ROOM DETAILS
    // ------------------------------------------------------------------
    doc.Add(new Paragraph("\nROOM DETAILS\n", bold));

    PdfPTable roomTbl = new PdfPTable(3);
    roomTbl.WidthPercentage = 100;
    roomTbl.AddCell(Header("Date"));
    roomTbl.AddCell(Header("Choice 1"));
    roomTbl.AddCell(Header("Choice 2"));

    foreach (DataRow r in dtRooms.Rows)
    {
        roomTbl.AddCell(Cell(r["BEGDA"].ToString(), normal));
        roomTbl.AddCell(Cell($"{r["ROOM_NO1"]}-{roomTypeDescr}", normal));
        roomTbl.AddCell(Cell($"{r["ROOM_NO2"]}-{roomTypeDescr}", normal));
    }

    doc.Add(roomTbl);

    // ------------------------------------------------------------------
    // FAMILY DETAILS
    // ------------------------------------------------------------------
    doc.Add(new Paragraph("\nFAMILY DETAILS\n", bold));

    PdfPTable famTbl = new PdfPTable(4);
    famTbl.WidthPercentage = 100;

    famTbl.AddCell(Header("Name"));
    famTbl.AddCell(Header("Relationship"));
    famTbl.AddCell(Header("Age"));
    famTbl.AddCell(Header("Gender"));

    foreach (DataRow f in dtFamily.Rows)
    {
        string rel = MapRelation(f["RELATION_CODE"].ToString());
        string gender = MapGender(f["GENDER_CODE"].ToString());

        famTbl.AddCell(Cell(f["NAME"].ToString(), normal));
        famTbl.AddCell(Cell(rel, normal));
        famTbl.AddCell(Cell(f["AGE"].ToString(), normal));
        famTbl.AddCell(Cell(gender, normal));
    }

    doc.Add(famTbl);

    // ------------------------------------------------------------------
    // MEMBER DETAILS
    // ------------------------------------------------------------------
    doc.Add(new Paragraph("\nMEMBER DETAILS\n", bold));

    PdfPTable memTbl = new PdfPTable(3);
    memTbl.WidthPercentage = 100;

    memTbl.AddCell(Header($"Child: {childCount}"));
    memTbl.AddCell(Header($"Adult: {adultCount}"));
    memTbl.AddCell(Header($"Total No. Of persons: {dtFamily.Rows.Count}"));

    doc.Add(memTbl);

    // Total Charges
    doc.Add(new Paragraph($"Total Charges: Rs.{totalCharges}", bold));
    doc.Add(new Paragraph("\n"));

    // ------------------------------------------------------------------
    // WITH REGARDS + CONTACT PERSON
    // ------------------------------------------------------------------
    PdfPTable regTbl = new PdfPTable(2);
    regTbl.WidthPercentage = 100;
    regTbl.SetWidths(new float[] { 50, 50 });

    regTbl.AddCell(new PdfPCell(new Phrase("With Regards,\nArea Manager,Employment Bureau\n\n(Group HR/IR)", normal))
    {
        Border = 0,
        HorizontalAlignment = Element.ALIGN_LEFT
    });

    regTbl.AddCell(new PdfPCell(new Phrase("Contact Person for Holiday Home\nBIBHU RANJAN MISHRA,9827104012", bold))
    {
        Border = 0,
        HorizontalAlignment = Element.ALIGN_RIGHT
    });

    doc.Add(regTbl);

    // ------------------------------------------------------------------
    // TERMS & CONDITIONS
    // ------------------------------------------------------------------
    doc.Add(new Paragraph("\n*** SINCE IT IS AN ELECTRONICALLY GENERATED PERMIT SO SIGNATURE IS NOT REQUIRED ***\n", bold));

    doc.Add(new Paragraph("\nTerms And Conditions\n", bold));
    doc.Add(new Paragraph(
        "* The employee in whose name the Holiday Home is booked must carry an identity proof.\n" +
        "* Occupancy strictly as per permit.\n" +
        "* Use of alcohol in the premises is strictly prohibited.\n" +
        "* Employees are advised to cancel the booking at least 15 days before.\n" +
        "* Late checkout attracts retention charges.\n" +
        "* Any damage to property must be compensated.\n",
        normal
    ));

    doc.Close();
    return pdfPath;
}

// ----------------------------------------------------------------------
// HELPER CELLS
// ----------------------------------------------------------------------
private PdfPCell Cell(string text, Font f)
{
    return new PdfPCell(new Phrase(text, f)) { Border = Rectangle.NO_BORDER, Padding = 4 };
}

private PdfPCell Header(string text)
{
    return new PdfPCell(new Phrase(text, FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10)))
    {
        BackgroundColor = new BaseColor(245, 245, 245),
        Padding = 4
    };
}

public string GetTotalCharges(string receiptNo)
{
    string sql = "SELECT TotalCharges FROM App_HDH_Booking WHERE ReceiptNo=@ReceiptNo";
    Dictionary<string, object> p = new Dictionary<string, object>();
    p.Add("ReceiptNo", receiptNo);
    DataHelper dh = new DataHelper();
    return dh.GetValue(sql, p).ToString();
}




this is my pdf generation code 
 
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

            iTextSharp.text.Image logo1 = iTextSharp.text.Image.GetInstance(@"C:\Cybersoft_Doc\Images\logo1.jpg");
            logo1.ScaleAbsolute(90, 35);

            iTextSharp.text.Image logo2 = iTextSharp.text.Image.GetInstance(@"C:\Cybersoft_Doc\Images\logo2.png");
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


Phone No and Department query       
 public DataSet GetData(string pno)
         {

            string strsql = "select ema_perno as PNo,ema_ename as Name,ema_dept_desc as Department,ema_phone_no  as Phone from SAPHRDB.dbo.T_EMPL_ALL where ema_perno = @ema_perno ";
            Dictionary<string, object> objParam = new Dictionary<string, object>();
            DataHelper dh = new DataHelper();
            objParam.Add("ema_perno", pno);
            return dh.GetDataset(strsql, "T_EMPL_ALL", objParam);
         }

checkInTime and CheckOutTime query

 public DataSet Get_ChkInChkOutTime(string Gcode)
        {

            string strsql = "select Left(Code_Desc,CHARINDEX('-',Code_Desc)-1) as checkIn,SUBSTRING(Code_Desc,CHARINDEX('-',Code_Desc) + 1,LEN(Code_Desc)) as checkOut from jehpdb.dbo.App_HTIME_Master where Code=@Code";
            Dictionary<string, object> objParam = new Dictionary<string, object>();
            DataHelper dh = new DataHelper();
            objParam.Add("Code", Gcode);
            return dh.GetDataset(strsql, "App_HTIME_Master", objParam);

        }

Room type query to show 2 bedded AC Room

 public DataSet Get_RoomTyp_Descrptn(string RoomTyp)
        {

            string strsql = "select RoomTypeDescription from App_HDH_RoomTypeMaster where RoomType=@RoomType";
            Dictionary<string, object> objParam = new Dictionary<string, object>();
            DataHelper dh = new DataHelper();
            objParam.Add("RoomType", RoomTyp);
            return dh.GetDataset(strsql, "App_HDH_RoomTypeMaster", objParam);

        }
