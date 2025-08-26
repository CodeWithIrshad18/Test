SqlCommand cmd = new SqlCommand(@"
    INSERT INTO App_MaterialRateChange
        (MaterialID, RequestedRate, RequestedBy, RequestedDate, Approver1Status, FinalStatus, Unit)
    OUTPUT INSERTED.ID
    VALUES
        (@MaterialID, @RequestedRate, @RequestedBy, @RequestedDate, @Approver1Status, @FinalStatus, @Unit);
", sql_con1);

cmd.Parameters.AddWithValue("@MaterialID", row["MaterialID"]);
cmd.Parameters.AddWithValue("@RequestedRate", row["RequestedRate"]);
cmd.Parameters.AddWithValue("@RequestedBy", row["RequestedBy"]);
cmd.Parameters.AddWithValue("@RequestedDate", row["RequestedDate"]);
cmd.Parameters.AddWithValue("@Approver1Status", row["Approver1Status"]);
cmd.Parameters.AddWithValue("@FinalStatus", row["FinalStatus"]);
cmd.Parameters.AddWithValue("@Unit", row["Unit"]);

object insertedId = cmd.ExecuteScalar();

// fetch if needed
DataTable DT = new DataTable();
using (SqlDataAdapter da = new SqlDataAdapter("SELECT * FROM App_MaterialRateChange WHERE ID = @ID", sql_con1))
{
    da.SelectCommand.Parameters.AddWithValue("@ID", insertedId);
    da.Fill(DT);
}



string str_sql_con = ConfigurationManager.ConnectionStrings["connect"].ToString();
                using (SqlConnection sql_con1 = new SqlConnection(str_sql_con))
                {
                    //string UID = Membership.GetUser(RegisterUser.UserName).ProviderUserKey.ToString();
                    sql_con1.Open();
                    foreach (DataRow row in dt.Rows)
                    {
			--this part insert the details and i want to fetch the id of inserted 

                        SqlCommand cmd = new SqlCommand("insert into App_MaterialRateChange(ID,MaterialID,RequestedRate,RequestedBy,RequestedDate,Approver1Status,FinalStatus,Unit)" +
                            "  values(@ID,@MaterialID,@RequestedRate,@RequestedBy,@RequestedDate,@Approver1Status,@FinalStatus,@Unit)", sql_con1);
                        cmd.Parameters.AddWithValue("@ID", row["ID"]);
                        cmd.Parameters.AddWithValue("@MaterialID", row["MaterialID"]);
                        cmd.Parameters.AddWithValue("@RequestedRate", row["RequestedRate"]);
                        cmd.Parameters.AddWithValue("@RequestedBy", row["RequestedBy"]);
                        cmd.Parameters.AddWithValue("@RequestedDate", row["RequestedDate"]);
                        cmd.Parameters.AddWithValue("@Approver1Status", row["Approver1Status"]);
                        cmd.Parameters.AddWithValue("@FinalStatus", row["FinalStatus"]);
                        cmd.Parameters.AddWithValue("@Unit", row["Unit"]);
                        cmd.Connection = sql_con1;

                        cmd.ExecuteNonQuery();

			in this line i want to fetch Id it gaves me error error in primary key violation 

                        object insertedId = cmd.ExecuteScalar();
                        DataTable DT = new DataTable();
                        using (SqlDataAdapter da = new SqlDataAdapter("select * from App_MaterialRateChange where id = @ID", str_sql_con))
                        {
                            da.SelectCommand.Parameters.AddWithValue("@ID", insertedId);
                            da.Fill(DT);
                        }

                    }
                    

                    sql_con1.Close();
                }
                
