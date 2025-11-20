public string GenerateBookingPDF(
    string receiptNo,
    string pernr,
    string fromdt,
    string toDt,
    string location,
    string hotel,
    string empName)
{
    string folderPath = @"C:\Cybersoft_Doc\SCH";

    if (!Directory.Exists(folderPath))
        Directory.CreateDirectory(folderPath);

    string pdfPath = Path.Combine(folderPath, $"Permit_{receiptNo}.pdf");

    Document doc = new Document(PageSize.A4, 30, 30, 30, 30);
    PdfWriter.GetInstance(doc, new FileStream(pdfPath, FileMode.Create));
    doc.Open();

    // ------------------- FONTS -------------------
    var titleFont = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 16);
    var hFont = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 12);
    var nFont = FontFactory.GetFont(FontFactory.HELVETICA, 11);

    // ------------------- LOGO -------------------
    string LOGO_PATH = @"C:\YourLogoFolder\yourlogo.png"; // <== CHANGE THIS
    if (File.Exists(LOGO_PATH))
    {
        iTextSharp.text.Image logo = iTextSharp.text.Image.GetInstance(LOGO_PATH);
        logo.ScaleAbsolute(70, 70);
        logo.Alignment = Element.ALIGN_CENTER;
        doc.Add(logo);
    }

    // SPACE BELOW LOGO
    doc.Add(new Paragraph("\n"));

    // ------------------- MAIN TITLE -------------------
    Paragraph title = new Paragraph("Holiday Home Booking Permit", titleFont);
    title.Alignment = Element.ALIGN_CENTER;
    doc.Add(title);
    doc.Add(new Paragraph("\n"));

    // ------------------- PERMIT INFO BLOCK -------------------
    PdfPTable infoTable = new PdfPTable(2);
    infoTable.WidthPercentage = 100;
    infoTable.SpacingAfter = 15;
    infoTable.SetWidths(new float[] { 30f, 70f });

    void AddInfo(string lbl, string val)
    {
        PdfPCell c1 = new PdfPCell(new Phrase(lbl, hFont));
        c1.Border = Rectangle.NO_BORDER;
        infoTable.AddCell(c1);

        PdfPCell c2 = new PdfPCell(new Phrase(val, nFont));
        c2.Border = Rectangle.NO_BORDER;
        infoTable.AddCell(c2);
    }

    AddInfo("Receipt No:", receiptNo);
    AddInfo("Employee No:", pernr);
    AddInfo("Employee Name:", empName);
    AddInfo("Location:", location);
    AddInfo("Guest House:", hotel);
    AddInfo("Check In:", fromdt);
    AddInfo("Check Out:", toDt);

    doc.Add(infoTable);

    // ------------------- ROOM TABLE -------------------
    Paragraph rHead = new Paragraph("Room Details", hFont);
    rHead.Alignment = Element.ALIGN_LEFT;
    doc.Add(rHead);

    PdfPTable roomTbl = new PdfPTable(4);
    roomTbl.WidthPercentage = 100;
    roomTbl.SpacingBefore = 5;
    roomTbl.SetWidths(new float[] { 25f, 25f, 25f, 25f });

    void AddHeader(string text)
    {
        PdfPCell cell = new PdfPCell(new Phrase(text, hFont));
        cell.BackgroundColor = BaseColor.LIGHT_GRAY;
        cell.HorizontalAlignment = Element.ALIGN_CENTER;
        cell.Padding = 5;
        roomTbl.AddCell(cell);
    }

    void AddCell(string text)
    {
        PdfPCell cell = new PdfPCell(new Phrase(text, nFont));
        cell.HorizontalAlignment = Element.ALIGN_CENTER;
        cell.Padding = 5;
        roomTbl.AddCell(cell);
    }

    AddHeader("Date");
    AddHeader("Room No. 1");
    AddHeader("Room No. 2");
    AddHeader("Amount Deduct");

    DataTable dtRooms = GetRoomDetails(pernr, receiptNo);
    foreach (DataRow r in dtRooms.Rows)
    {
        AddCell(Convert.ToString(r["BEGDA"]));
        AddCell(Convert.ToString(r["ROOM_NO1"]));
        AddCell(Convert.ToString(r["ROOM_NO2"]));
        AddCell(Convert.ToString(r["AMT_DEDUCT"]));
    }

    doc.Add(roomTbl);
    doc.Add(new Paragraph("\n"));

    // ------------------- FAMILY TABLE -------------------
    Paragraph fHead = new Paragraph("Family Details", hFont);
    fHead.Alignment = Element.ALIGN_LEFT;
    doc.Add(fHead);

    PdfPTable famTbl = new PdfPTable(4);
    famTbl.WidthPercentage = 100;
    famTbl.SpacingBefore = 5;
    famTbl.SetWidths(new float[] { 40f, 20f, 20f, 20f });

    AddHeader("Name");
    AddHeader("Relation");
    AddHeader("Age");
    AddHeader("Gender");

    DataTable dtFamily = GetFamilyDetails(pernr, receiptNo);
    foreach (DataRow f in dtFamily.Rows)
    {
        AddCell(Convert.ToString(f["NAME"]));
        AddCell(Convert.ToString(f["RELATION_CODE"]));
        AddCell(Convert.ToString(f["AGE"]));
        AddCell(Convert.ToString(f["GENDER_CODE"]));
    }

    doc.Add(famTbl);
    doc.Add(new Paragraph("\n"));

    // ------------------- TERMS & CONDITIONS -------------------
    Paragraph tcHead = new Paragraph("Terms & Conditions", hFont);
    tcHead.SpacingBefore = 10;
    doc.Add(tcHead);

    string terms =
        "• Employee must carry company ID and this booking permit.\n" +
        "• Only allotted rooms and registered persons are allowed.\n" +
        "• Maintain cleanliness and discipline inside the Holiday Home.\n" +
        "• Alcohol consumption is strictly prohibited.\n" +
        "• Any damage to property must be compensated.\n" +
        "• Rules and instructions of Guest House staff must be followed.\n";

    Paragraph tcText = new Paragraph(terms, nFont);
    tcText.SpacingBefore = 5;
    doc.Add(tcText);

    // ------------------- FOOTER -------------------
    doc.Add(new Paragraph("\n\nSignature\n", nFont));

    doc.Close();

    return pdfPath;
}

 
 
 
 void SendMail(string Email, string body, string subject, string pdfPath)
 {

     using (var mail = new MailMessage())
     {
         mail.From = new MailAddress("automatic_mail@tatasteel.com");
         mail.To.Add(Email);
         mail.Body = body;
         mail.Subject = subject;
         mail.IsBodyHtml = true;

         // Attach PDF
         Attachment pdf = new Attachment(pdfPath);
         mail.Attachments.Add(pdf);

         using (var smtp = new SmtpClient("144.0.11.253", 25))
         {
             smtp.Timeout = 20000;
             smtp.Send(mail);
         }
     }



 }


        private string EmailBody(string strLocation, string fromdt, string Todt, string perno, string strHotel, string Email, string subject, string strEname,string receiptNo)
        {
           


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
            SendMail(Email, body, subject);
            return body;
        }


error : There is no argument given that corresponds to the required parameter 'pdfPath' of 'HDH_BOOKING_CONFIRMATION.SendMail(string, string, string, string)'
