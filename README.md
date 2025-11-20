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
