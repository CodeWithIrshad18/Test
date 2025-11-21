 private string EmailBody(string strLocation, string fromdt, string Todt, string perno, string strHotel, string Email, string subject, string strEname,string receiptNo)
        {

            string pdfPath = GenerateBookingPDF(receiptNo, perno, fromdt, Todt, strLocation, strHotel, strEname);

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

                    string pdfPath = GenerateBookingPDF(receiptNo, perno, fromdt, Todt, strLocation, strHotel, strEname);

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


error : There is no argument given that corresponds to the required parameter 'roomTypeCode' of 'HDH_BOOKING_CONFIRMATION.GenerateBookingPDF(string, string, string, string, string, string, string, string, string)'
