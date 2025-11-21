public DataTable GetRoomTypeDescription(string roomType)
{
    string sql = @"SELECT ROOM_TYPE_DESC
                   FROM App_HDH_RoomTypeMaster
                   WHERE ROOM_TYPE_CODE = :roomType";

    List<OracleParameter> param = new List<OracleParameter>()
    {
        new OracleParameter("roomType", roomType)
    };

    return OracleExecuteQuery(sql, param);
}

DataTable roomDesc = GetRoomTypeDescription(roomTypeCode);
string roomTypeText = roomDesc.Rows[0]["RoomTypeDescription"].ToString();



string roomTypeText = roomDesc.Rows.Count > 0 
    ? roomDesc.Rows[0]["ROOM_TYPE_DESC"].ToString() 
    : "N/A";




public DataTable GetRoomTypeDescription(string roomType)
{
    string sql = @"SELECT ROOM_TYPE_DESC
                   FROM App_HDH_RoomTypeMaster
                   WHERE ROOM_TYPE_CODE = :roomType";

    List<OracleParameter> param = new List<OracleParameter>()
    {
        new OracleParameter("roomType", roomType)
    };

    return OracleExecuteQuery(sql, param);
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

        // ---------------- HELPER METHODS ----------------

        private PdfPCell CreateCell(string text, Font font)
        {
            return new PdfPCell(new Phrase(text, font)) { Border = 0 };
        }

        private PdfPCell Header(string text)
        {
            return new PdfPCell(new Phrase(text, FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10)))
            { BackgroundColor = BaseColor.WHITE };
        }

        private string MapRelation(string code)
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
                default: return code;
            }
        }

        private string MapGender(string code)
        {
            return code == "1" ? "Male" :
                   code == "2" ? "Female" :
                   code;
        }


        public DataTable GetEmployeeDetails(string pernr)
        {
            string sql = @"SELECT ema_ename AS Name,
                          ema_dept_desc AS Department,
                          ema_phone_no AS Phone
                   FROM SAPHRDB.dbo.T_EMPL_ALL
                   WHERE ema_perno = :pernr";

            List<OracleParameter> param = new List<OracleParameter>()
    {
        new OracleParameter("pernr", pernr)
    };

            return OracleExecuteQuery(sql, param);
        }


        public DataTable GetCheckInOutTime(string gcode)
        {
            string sql = @"SELECT 
                     LEFT(Code_Desc, INSTR(Code_Desc, '-') - 1) AS CheckIn,
                     SUBSTR(Code_Desc, INSTR(Code_Desc, '-') + 1) AS CheckOut
                   FROM jehpdb.App_HTIME_Master
                   WHERE Code = :gcode";

            List<OracleParameter> param = new List<OracleParameter>()
    {
        new OracleParameter("gcode", gcode)
    };

            return OracleExecuteQuery(sql, param);
        }


        public DataTable GetRoomTypeDescription(string roomType)
        {
            string sql = @"SELECT RoomTypeDescription
                   FROM App_HDH_RoomTypeMaster
                   WHERE RoomType = :roomType";

            List<OracleParameter> param = new List<OracleParameter>()
    {
        new OracleParameter("roomType", roomType)
    };

            return OracleExecuteQuery(sql, param);
        }

        public DataTable GetCharges(string pernr, string receiptNo)
        {
            string sql = @"SELECT AMT_DEDUCT 
                   FROM CTDRDB.T_BOOKING_DETAILS
                   WHERE PERNR = :pernr AND RECEIPT_NO = :receiptNo";

            List<OracleParameter> param = new List<OracleParameter>()
    {
        new OracleParameter("pernr", pernr),
        new OracleParameter("receiptNo", receiptNo)
    };

            return OracleExecuteQuery(sql, param);
        }


        public decimal GetTotalCharges(string pernr, string receiptNo)
        {
            DataTable dt = GetCharges(pernr, receiptNo);

            if (dt.Rows.Count > 0 && dt.Rows[0]["AMT_DEDUCT"] != DBNull.Value)
                return Convert.ToDecimal(dt.Rows[0]["AMT_DEDUCT"]);

            return 0;
        }



        public DataTable GetRoomDetails(string pernr, string receiptNo)
        {
            string sql = @"select Distinct BD.BEGDA,BD.ROOM_NO1,BD.ROOM_NO2,BD.AMT_DEDUCT
                       from CTDRDB.T_BOOKING_DETAILS BD
                       left join CTDRDB.t_family_dtl FD 
                       on BD.RECEIPT_NO = FD.RECEIPT_NO
                       where BD.PERNR = :pernr 
                       and BD.RECEIPT_NO = :Recpt
                       order by BD.BEGDA";

            List<OracleParameter> param = new List<OracleParameter>()
        {
            new OracleParameter("pernr", pernr),
            new OracleParameter("Recpt", receiptNo)
        };

            return OracleExecuteQuery(sql, param);
        }
        public DataTable GetFamilyDetails(string pernr, string receiptNo)
        {
            string sql = @"select Distinct FD.NAME,FD.RELATION_CODE,FD.AGE,FD.GENDER_CODE,FD.SNO
                   from CTDRDB.T_BOOKING_DETAILS BD
                   left join CTDRDB.t_family_dtl FD 
                   on BD.RECEIPT_NO = FD.RECEIPT_NO
                   where BD.PERNR = :pernr 
                   and BD.RECEIPT_NO = :Recpt
                   order by FD.SNO";

            List<OracleParameter> param = new List<OracleParameter>()
    {
        new OracleParameter("pernr", pernr),
        new OracleParameter("Recpt", receiptNo)
    };

            return OracleExecuteQuery(sql, param);
        }


        public DataTable GetHeaderDetails(string pernr, string receiptNo)
        {
            string sql = @"select Distinct BD.GSTHOUSE_LOC_ID,BD.GSTHOUSE_ID,
                   FD.BEGDA,FD.ENDDA,BD.STAY_DAYS,BD.ROOM_NO1,BD.ROOM_NO2,BD.AMT_DEDUCT,
                   FD.NAME,FD.RELATION_CODE,FD.AGE,FD.GENDER_CODE 
                   from CTDRDB.T_BOOKING_DETAILS BD
                   left join CTDRDB.t_family_dtl FD 
                   on BD.RECEIPT_NO = FD.RECEIPT_NO
                   where BD.PERNR = :pernr 
                   and BD.RECEIPT_NO = :Recpt";

            List<OracleParameter> param = new List<OracleParameter>()
    {
        new OracleParameter("pernr", pernr),
        new OracleParameter("Recpt", receiptNo)
    };

            return OracleExecuteQuery(sql, param);
        }


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

                string roomTypeCode = dr["ROOM_TYPE_CODE"].ToString(); // example column
                string checkCode = dr["CHECK_CODE"].ToString();     // example column

                string status = GetOracleBookingChk(receiptNo, perno, fromdt, Todt);

                if (status == "B")
                {
                    DateTime dtMailDate = DateTime.Now;

                    UpdateSql(receiptNo, status, dtMailDate);

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

                    string subject = $"Holiday Home Booking Confirmed ({receiptNo})";
                    string body = EmailBody(strLocation, fromdt, Todt, perno, strHotel, Email, subject, strEname, receiptNo,roomTypeCode,checkCode);

                    //SendMail(Email, body, subject, pdfPath);

                    Update_StatusSql(receiptNo, status, 1);
                }
                else
                {
                    Update_StatusSql(receiptNo, status, 0);
                }
            }
        }

 private string EmailBody(string strLocation, string fromdt, string Todt, string perno, string strHotel, string Email, string subject, string strEname,string receiptNo,string roomType,string CheckCode)
        {

            string pdfPath = GenerateBookingPDF(receiptNo, perno, fromdt, Todt, strLocation, strHotel, strEname,roomType, CheckCode);

            string body = $@" <body style='margin:0; padding:0; background:#dce6f2; font-family:Arial, sans-serif;'>

<table width='100%' cellpadding='0' cellspacing='0' style='background:#dce6f2; padding:20px 0;'>
<tr>
<td align='center'>

    <table width='700' cellpadding='0' cellspacing='0' style='background:#ffffff; border:1px solid #b8c6d0;'>

        <tr>
            <td style='background:#e6eef6; padding:15px 20px; font-size:18px; font-weight:bold; color:#000; border-bottom:1px solid #c0ccd6;'>
                Holiday Home Booking Confirmed ({receiptNo})
            </td>
        </tr>

        <tr>
            <td style='padding:15px 20px; background:#f5f9fd; border-bottom:1px solid #cfd8e2;'>
                <table cellpadding='0' cellspacing='0'>
                    <tr>
                        <td style='vertical-align:middle;'>
                            <img src='https://cdn-icons-png.flaticon.com/512/2088/2088617.png' width='20' height='20' />
                        </td>
                        <td style='padding-left:10px; font-size:14px; color:#000;'>
                            Created &nbsp;&nbsp; <strong>{perno}</strong>
                        </td>
                    </tr>
                </table>
            </td>
        </tr>

        <tr>
            <td style='padding:25px 20px; background:#e6eef6; font-size:15px; color:#000;'>

                <p>Dear <strong>{strEname} ({perno})</strong>,</p>

                <p>Your application for Holiday Home booking at <strong>{strHotel}</strong>, {strLocation} 
                from <strong>{fromdt}</strong> to <strong>{Todt}</strong> is confirmed.</p>

                <p>Please carry a printout of the booking permit along with you during your visit.</p>

                <p>Kindly comply with the Terms &amp; Conditions given in the permit during your stay.</p>

                <p>Have a pleasant stay ahead!!</p>

                <p>With warm regards,<br/><br/>

                Area Manager,<br/><br/>
                Employment Bureau</p>

            </td>
        </tr>

        <tr>
            <td style='padding:15px 20px; background:#ffffff; border-top:1px solid #cfd8e2;'>
                <a href='' style='font-size:15px; color:#003399; text-decoration:underline;'>
                    Holiday Home Permit
                </a>
            </td>
        </tr>

    </table>

</td>
</tr>
</table>

</body>";
            SendMail(Email, body, subject,pdfPath);
            return body;
        }
