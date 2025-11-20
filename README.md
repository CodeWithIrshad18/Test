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

            // ➤ Generate PDF
            string pdfPath = GenerateBookingPDF(receiptNo, perno, fromdt, Todt, strLocation, strHotel, strEname);

            string subject = $"Holiday Home Booking Confirmed ({receiptNo})";
            string body = EmailBody(strLocation, fromdt, Todt, perno, strHotel, Email, subject, strEname, receiptNo);

            // ➤ Send mail with PDF attachment
            SendMailWithAttachment(Email, body, subject, pdfPath);

            Update_StatusSql(receiptNo, status, 1);
        }
        else
        {
            Update_StatusSql(receiptNo, status, 0);
        }
    }
}

public string GenerateBookingPDF(string receiptNo, string perno, string fromdt, string Todt,
                                 string location, string hotel, string empName)
{
    string pdfPath = Path.Combine(Path.GetTempPath(), $"Permit_{receiptNo}.pdf");

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

public void SendMailWithAttachment(string Email, string body, string subject, string pdfPath)
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

using Oracle.ManagedDataAccess.Client;
using System.Data;

public DataTable OracleExecuteQuery(string sql, List<OracleParameter> parameters)
{
    DataTable dt = new DataTable();

    using (OracleConnection conn = new OracleConnection(
        ConfigurationManager.ConnectionStrings["connect_HDH"].ConnectionString))
    {
        conn.Open();

        using (OracleCommand cmd = new OracleCommand(sql, conn))
        {
            cmd.BindByName = true;

            if (parameters != null)
            {
                foreach (var p in parameters)
                    cmd.Parameters.Add(p);
            }

            using (OracleDataAdapter da = new OracleDataAdapter(cmd))
            {
                da.Fill(dt);
            }
        }
    }

    return dt;
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




//Main query 
            string sql = "select Distinct BD.GSTHOUSE_LOC_ID,BD.GSTHOUSE_ID,FD.BEGDA,FD.ENDDA,BD.STAY_DAYS,BD.ROOM_NO1,BD.ROOM_NO2,BD.AMT_DEDUCT,FD.NAME,FD.RELATION_CODE,FD.AGE,FD.GENDER_CODE from CTDRDB.T_BOOKING_DETAILS BD left join CTDRDB.t_family_dtl FD on BD.RECEIPT_NO = FD.RECEIPT_NO where BD.PERNR =:pernr and BD.RECEIPT_NO=:Recpt";

//Room Details

 string sql_RoomDetails = "select Distinct BD.BEGDA,BD.ROOM_NO1,BD.ROOM_NO2,BD.AMT_DEDUCT from CTDRDB.T_BOOKING_DETAILS BD left join CTDRDB.t_family_dtl FD on BD.RECEIPT_NO = FD.RECEIPT_NO where BD.PERNR =:pernr and BD.RECEIPT_NO=:Recpt order by BD.BEGDA";

//Family Details

            string sql_FamilyDetails = "select Distinct FD.NAME,FD.RELATION_CODE,FD.AGE,FD.GENDER_CODE,FD.SNO from CTDRDB.T_BOOKING_DETAILS BD left join CTDRDB.t_family_dtl FD on BD.RECEIPT_NO = FD.RECEIPT_NO where BD.PERNR =:pernr and BD.RECEIPT_NO=:Recpt order by FD.SNO";



//Room Type
   string sql2 = "SELECT distinct ROOM_TYPE FROM CTDRDB.T_GHSE_DTLS where GSTHOUSE_ID= :GSTHOUSE_ID and LOC_ID=:GSTHOUSE_LOC_ID ";


//Connectionstring 


 <add name="connect_HDH" connectionString="Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=176.0.0.105)(PORT=1521))(CONNECT_DATA=(SID=cmdrdev)));User ID=CTDRTSUISL;Password=Jpeg#2025;" providerName="Oracle.ManagedDataAccess.Client"/>


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
         DateTime ? strMailDt =null;

         int mailStatus = 0;
         
         string status = GetOracleBookingChk(receiptNo, perno, fromdt, Todt);
         if(status=="B")
         {
            DateTime dtMailDate = DateTime.Now;
             UpdateSql(receiptNo, status, dtMailDate);
             string subject = $"Holiday Home Booking Confirmed ({receiptNo})";
             string body = EmailBody(strLocation, fromdt, Todt, perno, strHotel, Email, subject, strEname, receiptNo);
             SendMail(Email, body, subject);
             mailStatus = 1;
         }
         else
         {
             mailStatus = 0;
             
         }

         Update_StatusSql(receiptNo,status, mailStatus);
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

        void SendMail(string Email, string body, string subject)
        {

            using (var mail = new MailMessage())
            {
                mail.From = new MailAddress("automatic_mail@tatasteel.com");
                mail.To.Add("jyotisharma301016@gmail.com");
                mail.Body = body;
                mail.Subject = subject;
                mail.IsBodyHtml = true;

                using (var smtp = new SmtpClient("144.0.11.253", 25))
                {
                    smtp.Timeout = 20000;
                    smtp.Send(mail);
                }
            }


        }
