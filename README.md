using(SqlConnection con = new SqlConnection("YourConnection"))
{
    con.Open();
    SqlCommand cmd = new SqlCommand(query, con);

    DataTable dt = new DataTable();
    dt.Load(cmd.ExecuteReader());

    foreach(DataRow row in dt.Rows)
    {
        string id = row["ID"].ToString();
        string deptId = row["DepartmentID"].ToString();
        string hodMail = row["HODEmail"].ToString();

        // ==== Link with ID ====
        string link = $"http://yourdomain/HOD_Rating.aspx?id={id}";

        // ==== Mail Body ====
        string body = $"Dear HOD,<br/>Please submit rating.<br/><br/>Click here: <a href='{link}'>Rating Form</a>";

        // ==== Send mail ====
        MailMessage mail = new MailMessage();
        mail.To.Add(hodMail);
        mail.From = new MailAddress("no-reply@xxx.com");
        mail.Subject = "HOD Rating Pending";
        mail.IsBodyHtml = true;
        mail.Body = body;

        SmtpClient smtp = new SmtpClient("smtp server");
        smtp.Send(mail);
    }
}



SELECT 
    Rs.ReqNo AS BarricadingRequestNo,
    Rs.CreatedOn AS BarricadingRequestOn,
    Rs.CreatedBy AS BarricadingRequestBy,

    Dv.Division AS Division,
    Dp.Department AS Department,

    Rs.Start_Date AS StartDate,
    Rs.End_Date AS EndDate,

    Rs.GISNo AS GISApprovalNo,

    Rs.SafetyHead_Pno AS SafetyApprovedBy,
    Rs.SafetyHead_CreatedOn AS SafetyApprovedOn,

    Rs.SectionHead_Pno AS SectionalInchargeApprovedBy,
    Rs.SectionHead_CreatedOn AS SectionalInchargeApprovedOn,

    -- ✅ Show only when Status = 'Closed'
    CASE 
        WHEN Rs.Status = 'Closed' 
        THEN Rs.SafetyHead_CreatedOn 
        ELSE NULL 
    END AS RequestClosedOn,

    CASE 
        WHEN Rs.Status = 'Closed' 
        THEN Rs.SafetyHead_Pno 
        ELSE NULL 
    END AS RequestClosedBy

FROM App_Road_side_barricade_Entry RS
INNER JOIN App_DivisionMaster Dv 
    ON Dv.ID = RS.Info_DivisionID
INNER JOIN App_DepartmentMaster Dp 
    ON Dp.ID = RS.Info_DepartmentID
WHERE 
    Rs.Start_Date >= @FromDate
    AND Rs.End_Date <= @ToDate
ORDER BY Rs.Start_Date;

 
 
 

SELECT 
     Rs.ReqNo AS BarricadingRequestNo,
     Rs.CreatedOn AS BarricadingRequestOn,
     Rs.CreatedBy AS BarricadingRequestBy,

     Dv.Division AS Division,
     Dp.Department AS Department,

     Rs.Start_Date AS StartDate,
     Rs.End_Date AS EndDate,

     Rs.GISNo AS GISApprovalNo,

     Rs.SafetyHead_Pno AS SafetyApprovedBy,
     Rs.SafetyHead_CreatedOn AS SafetyApprovedOn,

     Rs.SectionHead_Pno AS SectionalInchargeApprovedBy,
     Rs.SectionHead_CreatedOn AS SectionalInchargeApprovedOn,

     Rs.SafetyHead_CreatedOn AS RequestClosedOn,
     Rs.SafetyHead_Pno AS RequestClosedBy

 FROM App_Road_side_barricade_Entry RS
 INNER JOIN App_DivisionMaster Dv 
     ON Dv.ID = RS.Info_DivisionID
 INNER JOIN App_DepartmentMaster Dp 
     ON Dp.ID = RS.Info_DepartmentID
 WHERE 
     Rs.Start_Date >= @FromDate
     AND Rs.End_Date <= @ToDate
 ORDER BY Rs.Start_Date
