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
