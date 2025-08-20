private DataTable GetGeoData(string fromDate, string toDate, string department, string type, string attemptRange, string employeeType)
{
    bool isPunchIn = type == "PUNCH IN";
    bool isPunchOut = type == "PUNCH OUT";
    bool isBoth = string.IsNullOrEmpty(type);

    string selectClause = isBoth
        ? "DE.Pno, Emp.Ema_Ename, DE.DateAndTime, DE.PunchIn_FailedCount, DE.PunchOut_FailedCount"
        : isPunchIn
            ? "DE.Pno, Emp.Ema_Ename, DE.DateAndTime, DE.PunchIn_FailedCount"
            : "DE.Pno, Emp.Ema_Ename, DE.DateAndTime, DE.PunchOut_FailedCount";

    string query = $@"
        SELECT {selectClause}
        FROM App_FaceVerification_Details AS DE
        INNER JOIN SAPHRDB.dbo.T_Empl_All AS Emp
            ON DE.Pno COLLATE DATABASE_DEFAULT = Emp.ema_perno COLLATE DATABASE_DEFAULT
        WHERE CAST(DE.DateAndTime AS DATE) BETWEEN @FromDate AND @ToDate
    ";

    // Employee Type filter
    if (!string.IsNullOrEmpty(employeeType))
    {
        if (employeeType == "NON OPR")
        {
            query += " AND Emp.ema_pyrl_area IN ('JS','ZZ')";
        }
        else if (employeeType == "OPR")
        {
            query += " AND Emp.ema_pyrl_area NOT IN ('JS','ZZ')";
        }
        // if none selected → no condition added
    }

    if (!string.IsNullOrEmpty(department))
    {
        query += " AND Emp.ema_dept_desc = @Department";
    }

    int minAttempt = 0, maxAttempt = 0;
    if (!string.IsNullOrEmpty(attemptRange) && attemptRange.Contains("-"))
    {
        var parts = attemptRange.Split('-');
        minAttempt = int.Parse(parts[0]);
        maxAttempt = parts[1].ToLower() == "above" ? int.MaxValue : int.Parse(parts[1]);

        if (isPunchIn)
            query += " AND DE.PunchIn_FailedCount BETWEEN @MinAttempt AND @MaxAttempt";
        else if (isPunchOut)
            query += " AND DE.PunchOut_FailedCount BETWEEN @MinAttempt AND @MaxAttempt";
        else
            query += " AND (DE.PunchIn_FailedCount BETWEEN @MinAttempt AND @MaxAttempt OR DE.PunchOut_FailedCount BETWEEN @MinAttempt AND @MaxAttempt)";
    }

    query += " ORDER BY DE.DateAndTime";

    DataTable dt = new DataTable();

    using (SqlConnection con = new SqlConnection(System.Configuration.ConfigurationManager.ConnectionStrings["dbcs"].ConnectionString))
    {
        using (SqlCommand cmd = new SqlCommand(query, con))
        {
            cmd.Parameters.AddWithValue("@FromDate", fromDate);
            cmd.Parameters.AddWithValue("@ToDate", toDate);

            if (!string.IsNullOrEmpty(department))
                cmd.Parameters.AddWithValue("@Department", department);

            if (maxAttempt > 0)
            {
                cmd.Parameters.AddWithValue("@MinAttempt", minAttempt);
                cmd.Parameters.AddWithValue("@MaxAttempt", maxAttempt);
            }

            using (SqlDataAdapter sda = new SqlDataAdapter(cmd))
            {
                sda.Fill(dt);
            }
        }
    }

    // add OnlyDate column after filling
    if (!dt.Columns.Contains("OnlyDate"))
        dt.Columns.Add("OnlyDate", typeof(DateTime));

    foreach (DataRow row in dt.Rows)
    {
        row["OnlyDate"] = ((DateTime)row["DateAndTime"]).Date;
    }

    return dt;
}



i have this new dropdown to filter out Employee Type 
                           
   <div class="form-group col-md-4 mb-1">
                                   <label for="Employee Type" class="m-0 mr-2 p-0 col-form-label-sm col-sm-3 font-weight-bold fs-6">Employee Type:</label>
                                  <asp:DropDownList ID="DropDownList3" runat="server" CssClass="form-control form-control-sm col-sm-8" AutoPostBack="false">
    <asp:ListItem Text="-- Select Type --" Value="" />
    <asp:ListItem Text="OPR" Value="OPR" />
    <asp:ListItem Text="NON OPR" Value="NON OPR" />   
</asp:DropDownList>

                                  </div>

when user select NON OPR it filter using column ema_pyrl_area which has value JS and ZZ and if the User select OPR then leave these value and Rest and if none is selected then shows all

    private DataTable GetGeoData(string fromDate, string toDate, string department, string type, string attemptRange)
    {
        bool isPunchIn = type == "PUNCH IN";
        bool isPunchOut = type == "PUNCH OUT";
        bool isBoth = string.IsNullOrEmpty(type);


        string selectClause = isBoth
            ? "DE.Pno, Emp.Ema_Ename, DE.DateAndTime, DE.PunchIn_FailedCount, DE.PunchOut_FailedCount"
            : isPunchIn
                ? "DE.Pno, Emp.Ema_Ename, DE.DateAndTime, DE.PunchIn_FailedCount"
                : "DE.Pno, Emp.Ema_Ename, DE.DateAndTime, DE.PunchOut_FailedCount";

        string query = $@"
    SELECT {selectClause}
    FROM App_FaceVerification_Details AS DE
    INNER JOIN SAPHRDB.dbo.T_Empl_All AS Emp
        ON DE.Pno COLLATE DATABASE_DEFAULT = Emp.ema_perno COLLATE DATABASE_DEFAULT
    WHERE Emp.Ema_pyrl_area ='JS' and CAST(DE.DateAndTime AS DATE) BETWEEN @FromDate AND @ToDate
";

        if (!string.IsNullOrEmpty(department))
        {
            query += " AND Emp.ema_dept_desc = @Department";
        }


        int minAttempt = 0, maxAttempt = 0;
        if (!string.IsNullOrEmpty(attemptRange) && attemptRange.Contains("-"))
        {
            var parts = attemptRange.Split('-');
            minAttempt = int.Parse(parts[0]);
            maxAttempt = parts[1].ToLower() == "above" ? int.MaxValue : int.Parse(parts[1]);

            if (isPunchIn)
                query += " AND DE.PunchIn_FailedCount BETWEEN @MinAttempt AND @MaxAttempt";
            else if (isPunchOut)
                query += " AND DE.PunchOut_FailedCount BETWEEN @MinAttempt AND @MaxAttempt";
            else
                query += " AND (DE.PunchIn_FailedCount BETWEEN @MinAttempt AND @MaxAttempt OR DE.PunchOut_FailedCount BETWEEN @MinAttempt AND @MaxAttempt)";
        }

        query += " ORDER BY DE.DateAndTime";

        DataTable dt = new DataTable();

        dt.Columns.Add("OnlyDate", typeof(DateTime));
        foreach (DataRow row in dt.Rows)
        {
            row["OnlyDate"] = ((DateTime)row["DateAndTime"]).Date;
        }

        using (SqlConnection con = new SqlConnection(System.Configuration.ConfigurationManager.ConnectionStrings["dbcs"].ConnectionString))
        {
            using (SqlCommand cmd = new SqlCommand(query, con))
            {
                cmd.Parameters.AddWithValue("@FromDate", fromDate);
                cmd.Parameters.AddWithValue("@ToDate", toDate);

                if (!string.IsNullOrEmpty(department))
                    cmd.Parameters.AddWithValue("@Department", department);

                if (maxAttempt > 0)
                {
                    cmd.Parameters.AddWithValue("@MinAttempt", minAttempt);
                    cmd.Parameters.AddWithValue("@MaxAttempt", maxAttempt);
                }

                using (SqlDataAdapter sda = new SqlDataAdapter(cmd))
                {
                    sda.Fill(dt);
                }
            }
        }

        return dt;
    }
