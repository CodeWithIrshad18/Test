using System;
using System.Collections.Generic;
using System.IO;
using iTextSharp.text;
using iTextSharp.text.pdf;

namespace MyScheduler.Pdf
{
    public class BookingHeader
    {
        public string Name { get; set; }
        public string PersonalNo { get; set; }
        public string Department { get; set; }
        public string Mobile { get; set; }
        public string Location { get; set; }
        public string GuestHouse { get; set; }
        public string CheckInDate { get; set; }
        public string CheckInTime { get; set; }
        public string CheckOutDate { get; set; }
        public string CheckOutTime { get; set; }
        public string Duration { get; set; }
        public string ReceiptNo { get; set; }
    }

    public class RoomDetail
    {
        public string Date { get; set; }
        public string Choice1 { get; set; }
        public string Choice2 { get; set; } = "";
    }

    public class FamilyDetail
    {
        public string Name { get; set; }
        public string Relationship { get; set; }
        public string Age { get; set; }
        public string Gender { get; set; }
    }

    public static class GenerateHolidayHomePDF
    {
        /// <summary>
        /// Generate the Holiday Home Permit PDF as byte[]
        /// </summary>
        public static byte[] Generate(BookingHeader hdr, List<RoomDetail> rooms, List<FamilyDetail> family,
                                      int adultCount, int childCount, decimal totalCharges,
                                      string logoUrlOrPath = null)
        {
            using (var ms = new MemoryStream())
            {
                // Document size and margins tuned to look like a portrait page with side padding.
                var doc = new Document(PageSize.A4, 36, 36, 36, 36);
                var writer = PdfWriter.GetInstance(doc, ms);
                doc.Open();

                // Fonts (use built-in Helvetica -- Arial-like)
                var fTitle = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 16);
                var fSubTitle = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 12);
                var fNormal = FontFactory.GetFont(FontFactory.HELVETICA, 10);
                var fBold = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 10);
                var fSmall = FontFactory.GetFont(FontFactory.HELVETICA, 8);
                var fRedSmall = FontFactory.GetFont(FontFactory.HELVETICA_BOLD, 9, BaseColor.RED);
                var borderColor = new BaseColor(192, 204, 214);
                var lightBg = new BaseColor(230, 238, 246);

                // Top header block: logo left, title center, small logo right
                var headerTable = new PdfPTable(3) { WidthPercentage = 100 };
                headerTable.SetWidths(new float[] { 1f, 4f, 1f });

                // Left logo cell
                PdfPCell leftLogoCell = new PdfPCell() { Border = PdfPCell.NO_BORDER, Padding = 4 };
                if (!string.IsNullOrEmpty(logoUrlOrPath))
                {
                    try
                    {
                        Image logo = Image.GetInstance(logoUrlOrPath);
                        logo.ScaleToFit(60f, 60f);
                        leftLogoCell.AddElement(logo);
                    }
                    catch
                    {
                        leftLogoCell.Phrase = new Phrase(" ", fNormal);
                    }
                }
                else
                {
                    leftLogoCell.Phrase = new Phrase(" ", fNormal);
                }
                headerTable.AddCell(leftLogoCell);

                // Title cell (center)
                var titleCell = new PdfPCell();
                titleCell.Border = PdfPCell.NO_BORDER;
                titleCell.Padding = 4;
                titleCell.HorizontalAlignment = Element.ALIGN_CENTER;
                titleCell.AddElement(new Paragraph("Permit for Accomodation at Holiday Home", fTitle));
                titleCell.AddElement(new Paragraph($"Receipt No.: {hdr?.ReceiptNo ?? ""}", fBold));
                headerTable.AddCell(titleCell);

                // Right logo (small) cell
                PdfPCell rightLogoCell = new PdfPCell() { Border = PdfPCell.NO_BORDER, Padding = 4, HorizontalAlignment = Element.ALIGN_RIGHT };
                // optionally same logo or empty
                if (!string.IsNullOrEmpty(logoUrlOrPath))
                {
                    try
                    {
                        Image logo2 = Image.GetInstance(logoUrlOrPath);
                        logo2.ScaleToFit(50f, 50f);
                        rightLogoCell.AddElement(logo2);
                    }
                    catch
                    {
                        rightLogoCell.Phrase = new Phrase(" ", fNormal);
                    }
                }
                headerTable.AddCell(rightLogoCell);

                doc.Add(headerTable);
                doc.Add(Chunk.NEWLINE);

                // Personal details table (two-column layout within a table)
                var infoTable = new PdfPTable(2) { WidthPercentage = 100 };
                infoTable.SetWidths(new float[] { 1f, 1f });

                // left column block
                var leftBlock = new PdfPTable(2) { WidthPercentage = 100 };
                leftBlock.SetWidths(new float[] { 0.35f, 0.65f });
                AddKeyValueCell(leftBlock, "Name:", hdr?.Name, fBold, fNormal);
                AddKeyValueCell(leftBlock, "Department:", hdr?.Department, fBold, fNormal);
                AddKeyValueCell(leftBlock, "Location:", hdr?.Location, fBold, fNormal);
                AddKeyValueCell(leftBlock, "CheckIn Date:", hdr?.CheckInDate, fBold, fNormal);
                AddKeyValueCell(leftBlock, "CheckIn Time:", hdr?.CheckInTime, fBold, fNormal);
                AddKeyValueCell(leftBlock, "Duration:", hdr?.Duration, fBold, fNormal);

                var leftCell = new PdfPCell(leftBlock) { Border = PdfPCell.NO_BORDER, Padding = 4 };
                infoTable.AddCell(leftCell);

                // right column block
                var rightBlock = new PdfPTable(2) { WidthPercentage = 100 };
                rightBlock.SetWidths(new float[] { 0.35f, 0.65f });
                AddKeyValueCell(rightBlock, "Personal No.:", hdr?.PersonalNo, fBold, fNormal);
                AddKeyValueCell(rightBlock, "Mobile No.:", hdr?.Mobile, fBold, fNormal);
                AddKeyValueCell(rightBlock, "Guest House:", hdr?.GuestHouse, fBold, fNormal);
                AddKeyValueCell(rightBlock, "CheckOut Date:", hdr?.CheckOutDate, fBold, fNormal);
                AddKeyValueCell(rightBlock, "CheckOut Time:", hdr?.CheckOutTime, fBold, fNormal);
                AddKeyValueCell(rightBlock, " ", " ", fBold, fNormal);

                var rightCell = new PdfPCell(rightBlock) { Border = PdfPCell.NO_BORDER, Padding = 4 };
                infoTable.AddCell(rightCell);

                // Add info table
                var outerInfoCell = new PdfPCell(infoTable) { BorderColor = borderColor, Padding = 6, BackgroundColor = BaseColor.WHITE };
                var borderWrap = new PdfPTable(1) { WidthPercentage = 100 };
                borderWrap.AddCell(outerInfoCell);
                doc.Add(borderWrap);

                doc.Add(Chunk.NEWLINE);

                // ROOM DETAILS title
                var roomTitle = new Paragraph("ROOM DETAILS", fSubTitle) { Alignment = Element.ALIGN_LEFT };
                doc.Add(roomTitle);
                doc.Add(Chunk.NEWLINE);

                // Room details table (3 columns)
                var roomTable = new PdfPTable(3) { WidthPercentage = 100 };
                roomTable.SetWidths(new float[] { 1.5f, 3f, 3f });
                AddTableHeaderCell(roomTable, "Date", fBold, borderColor, lightBg);
                AddTableHeaderCell(roomTable, "Choice 1", fBold, borderColor, lightBg);
                AddTableHeaderCell(roomTable, "Choice 2", fBold, borderColor, lightBg);

                if (rooms != null)
                {
                    foreach (var r in rooms)
                    {
                        AddTableCell(roomTable, r.Date, fNormal);
                        AddTableCell(roomTable, r.Choice1, fNormal);
                        AddTableCell(roomTable, r.Choice2 ?? "", fNormal);
                    }
                }

                doc.Add(roomTable);
                doc.Add(Chunk.NEWLINE);

                // FAMILY DETAILS title
                var famTitle = new Paragraph("FAMILY DETAILS", fSubTitle) { Alignment = Element.ALIGN_LEFT };
                doc.Add(famTitle);
                doc.Add(Chunk.NEWLINE);

                // Family details table (4 columns)
                var famTable = new PdfPTable(4) { WidthPercentage = 100 };
                famTable.SetWidths(new float[] { 3f, 2f, 1f, 1f });
                AddTableHeaderCell(famTable, "Name", fBold, borderColor, lightBg);
                AddTableHeaderCell(famTable, "Relationship", fBold, borderColor, lightBg);
                AddTableHeaderCell(famTable, "Age", fBold, borderColor, lightBg);
                AddTableHeaderCell(famTable, "Gender", fBold, borderColor, lightBg);

                if (family != null)
                {
                    foreach (var f in family)
                    {
                        AddTableCell(famTable, f.Name, fNormal);
                        AddTableCell(famTable, f.Relationship, fNormal);
                        AddTableCell(famTable, f.Age, fNormal);
                        AddTableCell(famTable, f.Gender, fNormal);
                    }
                }

                doc.Add(famTable);
                doc.Add(Chunk.NEWLINE);

                // MEMBER DETAILS summary block
                var summaryTable = new PdfPTable(3) { WidthPercentage = 100 };
                summaryTable.SetWidths(new float[] { 1f, 1f, 1f });

                var childText = new PdfPCell(new Phrase($"Child: {childCount}", fNormal)) { Border = PdfPCell.NO_BORDER, Padding = 6 };
                var adultText = new PdfPCell(new Phrase($"Adult: {adultCount}", fNormal)) { Border = PdfPCell.NO_BORDER, Padding = 6 };
                var totalText = new PdfPCell(new Phrase($"Total No. Of persons: {childCount + adultCount}", fNormal)) { Border = PdfPCell.NO_BORDER, Padding = 6 };

                summaryTable.AddCell(childText);
                summaryTable.AddCell(adultText);
                summaryTable.AddCell(totalText);

                var chargeRow = new PdfPTable(1) { WidthPercentage = 100 };
                var chargeCell = new PdfPCell(new Phrase($"Total Charges: Rs.{totalCharges}", fBold)) { Border = PdfPCell.NO_BORDER, Padding = 6 };
                chargeRow.AddCell(chargeCell);

                doc.Add(summaryTable);
                doc.Add(chargeRow);
                doc.Add(Chunk.NEWLINE);

                // Sign / contact block (two columns)
                var signTable = new PdfPTable(2) { WidthPercentage = 100 };
                signTable.SetWidths(new float[] { 1f, 1f });

                var leftSign = new PdfPCell(new Phrase("With Regards,\nArea Manager,Employment Bureau\n\n(Group HR/IR)", fNormal)) { Border = PdfPCell.NO_BORDER, Padding = 6 };
                var rightSign = new PdfPCell(new Phrase("Contact Person for Holiday Home\n\nBIBHU RANJAN MISHRA,9827104012", fNormal)) { Border = PdfPCell.NO_BORDER, Padding = 6, HorizontalAlignment = Element.ALIGN_RIGHT };

                signTable.AddCell(leftSign);
                signTable.AddCell(rightSign);

                doc.Add(signTable);
                doc.Add(Chunk.NEWLINE);

                // *** electronic signature note
                var note = new Paragraph("*** SINCE IT IS AN ELECTRONICALLY GENERATED PERMIT SO SIGNATURE IS NOT REQUIRED ***", fSmall) { Alignment = Element.ALIGN_CENTER };
                doc.Add(note);
                doc.Add(Chunk.NEWLINE);

                // Terms & Conditions title
                var tcTitle = new Paragraph("Terms And Conditions", fBold) { Alignment = Element.ALIGN_CENTER };
                doc.Add(tcTitle);
                doc.Add(Chunk.NEWLINE);

                // Terms & Conditions (red selective text and normal paragraphs)
                var terms = new Paragraph();
                terms.SpacingBefore = 6;
                terms.SpacingAfter = 6;
                terms.Add(new Chunk("* The employee in whose name the Holiday Home is booked must carry an identity proof (i.e Company's Gate-pass and Govt. ID Card for all family members) along with the permit and the same should be produced at the time of occupation of Holiday Home.\n\n", fNormal));
                terms.Add(new Chunk("* Occupancy is to be maintained strictly as per the permit issued to the employee concerned.\n\n", fNormal));
                terms.Add(new Chunk("* Use of alcohol in the premises is strictly prohibited.\n\n", fNormal));

                // big red paragraph (from screenshot)
                var bigRed = new Paragraph();
                bigRed.Add(new Chunk("* Employees are advised to cancel the booking in the system atleast 15 days prior to the start of Occupancy Date, if they are not availing the holiday home facility. If any employee fails to cancel the booking in the system, than the charges of booking will be deducted from the salary of employees and will not be refunded. Pls find below example for your better understanding.\nExample : if holiday home occupancy start date is 21 February 2026 and the employee cancels the booking on or before 06 February 2026, then they will get refund. If cancellation is done on 07 February 2026 or later, the booking amount will not be refunded.\n\n", fRedSmall));
                bigRed.Alignment = Element.ALIGN_LEFT;

                doc.Add(terms);
                doc.Add(bigRed);

                doc.Add(Chunk.NEWLINE);

                // Red footer notes block (two paragraphs)
                var footerRed1 = new Paragraph("* Late Check Out Retention Charges: If any employee is failed to check out as per due date and time of permit no. Employees needs to pay retention charges directly to hotel as per hotel terms & condition. If any employees fails to make payment directly to hotel, retention payment will be deducted from their salary as per request of hotel. Note: TSL will not entertain any request for waiver.\n\n", fRedSmall);
                var footerRed2 = new Paragraph("* Any damage of property, employee must be settle the matter while check out time at Hotel, If any employee fails to make payment directly to hotel, damage of losses will be deducted from their salary as per request of hotel. Note: TSL will not entertain any request for waiver.\n\n", fRedSmall);
                doc.Add(footerRed1);
                doc.Add(footerRed2);

                // Finalize
                doc.Close();
                writer.Close();

                return ms.ToArray();
            }
        }

        // small helper to add header cells
        private static void AddTableHeaderCell(PdfPTable table, string text, Font font, BaseColor borderColor, BaseColor bgColor)
        {
            var cell = new PdfPCell(new Phrase(text, font))
            {
                BackgroundColor = bgColor,
                BorderColor = borderColor,
                Padding = 6,
                HorizontalAlignment = Element.ALIGN_LEFT
            };
            table.AddCell(cell);
        }

        // small helper to add body cells
        private static void AddTableCell(PdfPTable table, string text, Font font)
        {
            var cell = new PdfPCell(new Phrase(text ?? "", font))
            {
                Padding = 6,
                HorizontalAlignment = Element.ALIGN_LEFT
            };
            table.AddCell(cell);
        }

        // add key-value small rows in the two-column details block
        private static void AddKeyValueCell(PdfPTable table, string key, string value, Font keyFont, Font valFont)
        {
            table.AddCell(new PdfPCell(new Phrase(key, keyFont)) { Border = PdfPCell.NO_BORDER, Padding = 3 });
            table.AddCell(new PdfPCell(new Phrase(value ?? "", valFont)) { Border = PdfPCell.NO_BORDER, Padding = 3 });
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




 protected void btnSearch_Click(object sender, EventArgs e)
        {
            BL_Permit_Report blobj = new BL_Permit_Report();
            DataSet ds = new DataSet();
            DataSet ds_Loc = new DataSet();
            DataSet ds_GLoc = new DataSet();
            DataSet ds_ChkInOutTime = new DataSet();
            string StrCond = string.Empty;
            StrCond = " 1=1 ";
            ReportDataSource rds = new ReportDataSource();
            ReportDataSource rds1 = new ReportDataSource();
            rds.Name = "DataSet1";
            rds1.Name = "DataSet2";
            string ReceiptNo = ddlReceiptNo.SelectedValue.ToString();
            string pno = Session["UserName"].ToString();
            Dictionary<string, object> ddlParams = new Dictionary<string, object>();

            //string RoomDetails = "select * from CTDRDB.T_BOOKING_DETAILS BD where BD.PERNR =:pernr and BD.RECEIPT_NO=:Recpt";
            //string FamilyDetails = "select * from CTDRDB.t_family_dtl FD where BD.PERNR =:pernr and BD.RECEIPT_NO=:Recpt";

            string sql = "select Distinct BD.GSTHOUSE_LOC_ID,BD.GSTHOUSE_ID,FD.BEGDA,FD.ENDDA,BD.STAY_DAYS,BD.ROOM_NO1,BD.ROOM_NO2,BD.AMT_DEDUCT,FD.NAME,FD.RELATION_CODE,FD.AGE,FD.GENDER_CODE from CTDRDB.T_BOOKING_DETAILS BD left join CTDRDB.t_family_dtl FD on BD.RECEIPT_NO = FD.RECEIPT_NO where BD.PERNR =:pernr and BD.RECEIPT_NO=:Recpt";
            string sql_RoomDetails = "select Distinct BD.BEGDA,BD.ROOM_NO1,BD.ROOM_NO2,BD.AMT_DEDUCT from CTDRDB.T_BOOKING_DETAILS BD left join CTDRDB.t_family_dtl FD on BD.RECEIPT_NO = FD.RECEIPT_NO where BD.PERNR =:pernr and BD.RECEIPT_NO=:Recpt order by BD.BEGDA";
            string sql_FamilyDetails = "select Distinct FD.NAME,FD.RELATION_CODE,FD.AGE,FD.GENDER_CODE,FD.SNO from CTDRDB.T_BOOKING_DETAILS BD left join CTDRDB.t_family_dtl FD on BD.RECEIPT_NO = FD.RECEIPT_NO where BD.PERNR =:pernr and BD.RECEIPT_NO=:Recpt order by FD.SNO";

            ddlParams.Add("pernr", Session["UserName"].ToString()); 
            ddlParams.Add("Recpt", ReceiptNo);

            DataSet ds1 = GetDataset_Oracle(sql, "App_HDH_Request", ddlParams);
            DataSet ds_RoomDetails = GetDataset_Oracle(sql_RoomDetails, "App_HDH_Request", ddlParams);
            DataSet ds_FamilyDetails = GetDataset_Oracle(sql_FamilyDetails, "App_HDH_Request", ddlParams);

            //Location
            string L_Code = ds1.Tables[0].Rows[0]["GSTHOUSE_LOC_ID"].ToString();
            ds_Loc = blobj.Get_Loc(L_Code);
            //Guest House
            string Gcode = ds1.Tables[0].Rows[0]["GSTHOUSE_ID"].ToString();
            ds_GLoc = blobj.Get_Guest_House_Loc(Gcode, L_Code);

            //ChkinTimeOut
            ds_ChkInOutTime = blobj.Get_ChkInChkOutTime(Gcode);
            Dictionary<string, object> ddlParams1 = new Dictionary<string, object>();
            //roomtype
            string sql2 = "SELECT distinct ROOM_TYPE FROM CTDRDB.T_GHSE_DTLS where GSTHOUSE_ID= :GSTHOUSE_ID and LOC_ID=:GSTHOUSE_LOC_ID ";
            ddlParams1.Add("GSTHOUSE_ID", Gcode);
            ddlParams1.Add("GSTHOUSE_LOC_ID", L_Code);

            DataSet ds2 = GetDataset_Oracle(sql2, "T_GHSE_DTLS", ddlParams1);

            // roomtypeDescription
            string RoomTyp = ds2.Tables[0].Rows[0]["ROOM_TYPE"].ToString();
            DataSet ds_RoomTyp = new DataSet();
            ds_RoomTyp = blobj.Get_RoomTyp_Descrptn(RoomTyp);


            ds = blobj.GetData(pno);
            if (ds.Tables[0].Rows.Count>0)
            {
                ReportParameter Name = new ReportParameter();
                ReportParameter Pno = new ReportParameter();
                ReportParameter Department = new ReportParameter();
                ReportParameter Mobile = new ReportParameter();
                ReportParameter Location = new ReportParameter();
                ReportParameter GuestHouse = new ReportParameter();
                ReportParameter ChkIn = new ReportParameter();
                ReportParameter ChkOut = new ReportParameter();
                ReportParameter Duration = new ReportParameter();
                ReportParameter ChkIn_Time = new ReportParameter();
                ReportParameter ChkOut_Time = new ReportParameter();
                ReportParameter Duration_Time = new ReportParameter();
                Name = new ReportParameter("Name", ds.Tables[0].Rows[0]["Name"].ToString(), true);
                Pno = new ReportParameter("Pno", ds.Tables[0].Rows[0]["PNo"].ToString(), true);
                Department = new ReportParameter("Department", ds.Tables[0].Rows[0]["Department"].ToString(), true);
                Mobile = new ReportParameter("Mobile", ds.Tables[0].Rows[0]["Phone"].ToString(), true);

                if (ds_Loc.Tables[0].Rows.Count > 0)
                {
                    Location = new ReportParameter("Location", ds_Loc.Tables[0].Rows[0]["Code_Desc"].ToString(), true);
                }
                if (ds_GLoc.Tables[0].Rows.Count > 0)
                {
                    GuestHouse = new ReportParameter("GuestHouse", ds_GLoc.Tables[0].Rows[0]["Code_Desc"].ToString(), true);
                }
                if(ds1.Tables[0].Rows.Count>0)
                {
                    ChkIn = new ReportParameter("ChkIn", ds1.Tables[0].Rows[0]["BEGDA"].ToString(), true);
                    ChkOut = new ReportParameter("ChkOut", ds1.Tables[0].Rows[0]["ENDDA"].ToString(), true);
                    Duration = new ReportParameter("Duration", ds1.Tables[0].Rows[0]["STAY_DAYS"].ToString(), true);
                }

               
                if(ds_ChkInOutTime.Tables[0].Rows.Count > 0)
                {
                    ChkIn_Time = new ReportParameter("ChkIn_Time", ds_ChkInOutTime.Tables[0].Rows[0]["checkIn"].ToString(), true);
                    ChkOut_Time = new ReportParameter("ChkOut_Time", ds_ChkInOutTime.Tables[0].Rows[0]["checkOut"].ToString(), true);
                }
               
                ReportParameter strReceiptNo = new ReportParameter("strReceiptNo", ddlReceiptNo.SelectedValue.ToString(), true);
                string Roomtype ="";
                ReportViewer1.Visible = true;
                ds2.Tables[0].Rows[0]["ROOM_TYPE"].ToString();
                if(ds2.Tables.Count>0 && ds2.Tables[0].Rows.Count>0)
                {
                    Roomtype = ds_RoomTyp.Tables[0].Rows[0]["RoomTypeDescription"].ToString();
                }
                
                foreach(DataRow dr in ds_RoomDetails.Tables[0].Rows)
                {
                    string r1 = dr["ROOM_NO1"].ToString();
                    string r2 = dr["ROOM_NO2"].ToString();
                    if(!string.IsNullOrEmpty(r1))
                    dr["ROOM_NO1"] = r1 + "-" + Roomtype;
                    if (!string.IsNullOrEmpty(r2))
                        dr["ROOM_NO2"] = r2 + "-" + Roomtype;
                }

                rds.Value = ds_RoomDetails.Tables[0];
                rds1.Value = ds_FamilyDetails.Tables[0];

                ReportViewer1.LocalReport.DataSources.Clear();

                ReportViewer1.LocalReport.DataSources.Add(rds);
                ReportViewer1.LocalReport.DataSources.Add(rds1);
                this.ReportViewer1.LocalReport.SetParameters(new ReportParameter[] { Name, Pno, Department, Mobile, strReceiptNo, Location, GuestHouse, ChkIn, ChkOut, Duration, ChkIn_Time, ChkOut_Time });

                this.ReportViewer1.LocalReport.Refresh();
            }



        }
