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
