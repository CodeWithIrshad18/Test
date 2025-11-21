full code 

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

        // ---------------- HELPER METHODS ----------------

        private PdfPCell CreateCell(string text, Font font)
        {
            return new PdfPCell(new Phrase(text, font)) { Border = 0 };
        }

        private PdfPCell Header(string text)
        {
            return new PdfPCell(new Phrase(text, FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10)))
            {
                BackgroundColor = BaseColor.WHITE,
                HorizontalAlignment = Element.ALIGN_LEFT
            };
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
