string roomTypeCode = dr["ROOM_TYPE_CODE"].ToString(); // example column
string checkCode    = dr["CHECK_CODE"].ToString();     // example column

string pdfPath = GenerateBookingPDF(
    receiptNo,
    perno,
    fromdt,
    Todt,
    strLocation,
    strHotel,
    strEname,
    roomTypeCode,
    checkCode
);




public void Booking_Confirmation_Mail()
        {
            DataTable requestes = GetSqlData();

            foreach (DataRow dr in requestes.Rows)
            {
                string receiptNo = dr["ReceiptNo"].ToString();
                string perno = dr["Pno"].ToString();
                string fromdt = Convert.ToString(dr["FromDt"]);
                string Todt = Convert.ToString(dr["ToDt"]);
                string Email = dr["Email_ID"].ToString();
                string strLocation = dr["Location"].ToString();
                string strHotel = dr["Hotel_Name"].ToString();
                string strEname = dr["EName"].ToString();

                string status = GetOracleBookingChk(receiptNo, perno, fromdt, Todt);

                if (status == "B")
                {
                    DateTime dtMailDate = DateTime.Now;

                    UpdateSql(receiptNo, status, dtMailDate);

                    string pdfPath = GenerateBookingPDF(receiptNo, perno, fromdt, Todt, strLocation, strHotel, strEname); --- error line 

                    string subject = $"Holiday Home Booking Confirmed ({receiptNo})";
                    string body = EmailBody(strLocation, fromdt, Todt, perno, strHotel, Email, subject, strEname, receiptNo);

                    //SendMail(Email, body, subject, pdfPath);

                    Update_StatusSql(receiptNo, status, 1);
                }
                else
                {
                    Update_StatusSql(receiptNo, status, 0);
                }
            }
        }


  public string GenerateBookingPDF(string receiptNo, string perno, string fromdt, string Todt, string location, string hotel, string empName, string roomTypeCode, string checkCode)
        {
            string folderpath = @"C:\Cybersoft_Doc\SCH";
            string pdfPath = Path.Combine(folderpath, $"Permit.pdf");

            Document doc = new Document(PageSize.A4, 40, 40, 40, 40);
            PdfWriter.GetInstance(doc, new FileStream(pdfPath, FileMode.Create));
            doc.Open();

            // ---------------- HEADER ----------------
            PdfPTable headerTbl = new PdfPTable(3);
            headerTbl.WidthPercentage = 100;
            headerTbl.SetWidths(new float[] { 25, 50, 25 });

            Image logo1 = Image.GetInstance(@"C:\Cybersoft_Doc\Images\logo1.jpg");
            logo1.ScaleAbsolute(90, 35);

            Image logo2 = Image.GetInstance(@"C:\Cybersoft_Doc\Images\logo2.jpg");
            logo2.ScaleAbsolute(70, 50);

            PdfPCell leftLogo = new PdfPCell(logo1) { Border = 0, HorizontalAlignment = Element.ALIGN_LEFT };
            PdfPCell rightLogo = new PdfPCell(logo2) { Border = 0, HorizontalAlignment = Element.ALIGN_RIGHT };

            Font titleFont = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 14);
            PdfPCell titleCell = new PdfPCell(new Phrase("Permit for Accommodation at Holiday Home", titleFont))
            {
                Border = 0,
                HorizontalAlignment = Element.ALIGN_CENTER
            };

            headerTbl.AddCell(leftLogo);
            headerTbl.AddCell(titleCell);
            headerTbl.AddCell(rightLogo);
            doc.Add(headerTbl);
            doc.Add(new Paragraph("\n"));

            // ---------------- FETCH EXTRA INFO ----------------
            DataTable empDt = GetEmployeeDetails(perno);
            string dept = empDt.Rows[0]["Department"].ToString();
            string phone = empDt.Rows[0]["Phone"].ToString();

            DataTable chkDt = GetCheckInOutTime(checkCode);
            string checkIn = chkDt.Rows[0]["CheckIn"].ToString();
            string checkOut = chkDt.Rows[0]["CheckOut"].ToString();

            DataTable roomDesc = GetRoomTypeDescription(roomTypeCode);
            string roomTypeText = roomDesc.Rows[0]["RoomTypeDescription"].ToString();

            decimal totalCharge = GetTotalCharges(perno, receiptNo);

            // ---------------- BASIC DETAILS TABLE ----------------
            Font normal = FontFactory.GetFont(FontFactory.HELVETICA, 10);
            Font bold = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10);

            PdfPTable detailTbl = new PdfPTable(2);
            detailTbl.WidthPercentage = 100;
            detailTbl.SetWidths(new float[] { 50, 50 });

            detailTbl.AddCell(CreateCell($"Name: {empName}", normal));
            detailTbl.AddCell(CreateCell($"Personal No.: {perno}", normal));

            detailTbl.AddCell(CreateCell($"Department: {dept}", normal));
            detailTbl.AddCell(CreateCell($"Phone No: {phone}", normal));

            detailTbl.AddCell(CreateCell($"Location: {location}", normal));
            detailTbl.AddCell(CreateCell($"Guest House: {hotel}", normal));

            detailTbl.AddCell(CreateCell($"Room Type: {roomTypeText}", normal));
            detailTbl.AddCell(CreateCell($"Total Charges: ₹{totalCharge}", bold));

            detailTbl.AddCell(CreateCell($"Check-In Date: {fromdt} ({checkIn})", normal));
            detailTbl.AddCell(CreateCell($"Check-Out Date: {Todt} ({checkOut})", normal));

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

            int childCount = 0, adultCount = 0;

            foreach (DataRow f in dtFamily.Rows)
            {
                string relation = MapRelation(f["RELATION_CODE"].ToString());
                string gender = MapGender(f["GENDER_CODE"].ToString());
                int age = Convert.ToInt32(f["AGE"]);

                if (age < 18) childCount++; else adultCount++;

                famTbl.AddCell(f["NAME"].ToString());
                famTbl.AddCell(relation);
                famTbl.AddCell(age.ToString());
                famTbl.AddCell(gender);
            }

            doc.Add(famTbl);

            // ---------------- MEMBER COUNT ----------------
            doc.Add(new Paragraph("\nMEMBER DETAILS\n", bold));

            PdfPTable memTbl = new PdfPTable(3);
            memTbl.WidthPercentage = 100;
            memTbl.AddCell(Header($"Child: {childCount}"));
            memTbl.AddCell(Header($"Adult: {adultCount}"));
            memTbl.AddCell(Header($"Total Persons: {dtFamily.Rows.Count}"));

            doc.Add(memTbl);

            doc.Add(new Paragraph("\n"));

            // ---------------- SIGNATURE BLOCK ----------------
            PdfPTable regardTbl = new PdfPTable(2);
            regardTbl.WidthPercentage = 100;

            regardTbl.AddCell(new PdfPCell(new Phrase("With Regards,\nArea Manager\n(Group HR/IR)", normal))
            { Border = 0 });

            regardTbl.AddCell(new PdfPCell(new Phrase("Contact Person:\nBIBHU RANJAN MISHRA\n📞 9827104012", bold))
            { Border = 0, HorizontalAlignment = Element.ALIGN_RIGHT });

            doc.Add(regardTbl);

            // ---------------- FOOTER ----------------
            doc.Add(new Paragraph("\n*** ELECTRONICALLY GENERATED — NO SIGNATURE REQUIRED ***\n", bold));

            doc.Close();
            return pdfPath;
        }


There is no argument given that corresponds to the required parameter 'roomTypeCode' of 'HDH_BOOKING_CONFIRMATION.GenerateBookingPDF(string, string, string, string, string, string, string, string, string)'	HDH_BOOKING_CONFIRMATION
