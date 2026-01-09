public class ResumeInfo
{
    public string QuestionId { get; set; }
    public string ModuleId { get; set; }
}


private ResumeInfo GetResumeInfo(int userId)
{
    ResumeInfo info = new ResumeInfo();

    string cs = ConfigurationManager.ConnectionStrings["ApplicationServices"].ConnectionString;

    using (SqlConnection con = new SqlConnection(cs))
    using (SqlCommand cmd = new SqlCommand(@"
        SELECT TOP 1 
            Q.Id AS QuestionId,
            Q.ModuleID
        FROM App_QuestionMaster Q
        JOIN App_Module_Master M ON M.ID = Q.ModuleID
        LEFT JOIN ASP_User_Response R
            ON R.QuestionID = Q.Id
            AND R.UserID = @UserID
        WHERE Q.IsActive = 1
          AND M.IsActive = 1
          AND R.QuestionID IS NULL
        ORDER BY M.SerialNo, Q.SeqNo
    ", con))
    {
        cmd.Parameters.AddWithValue("@UserID", userId);
        con.Open();

        using (var reader = cmd.ExecuteReader())
        {
            if (reader.Read())
            {
                info.QuestionId = reader["QuestionId"].ToString();
                info.ModuleId = reader["ModuleID"].ToString();
            }
        }
    }

    return info;
}

ResumeInfo resumeInfo = GetResumeInfo(userId);
    
    
    
    private (string QuestionId, string ModuleId) GetResumeInfo(int userId)
    {
        string cs = ConfigurationManager.ConnectionStrings["ApplicationServices"].ConnectionString;

        using (SqlConnection con = new SqlConnection(cs))
        using (SqlCommand cmd = new SqlCommand(@"
    SELECT TOP 1 
        Q.Id AS QuestionId,
        Q.ModuleID
    FROM App_QuestionMaster Q
    JOIN App_Module_Master M ON M.ID = Q.ModuleID
    LEFT JOIN ASP_User_Response R
        ON R.QuestionID = Q.Id
        AND R.UserID = @UserID
    WHERE Q.IsActive = 1
      AND M.IsActive = 1
      AND R.QuestionID IS NULL
    ORDER BY M.SerialNo, Q.SeqNo
", con))
        {
            cmd.Parameters.AddWithValue("@UserID", userId);
            con.Open();

            using (var reader = cmd.ExecuteReader())
            {
                if (reader.Read())
                {
                    return (
                        reader["QuestionId"].ToString(),
                        reader["ModuleID"].ToString()
                    );
                }
            }
        }

        return (null, null);
    }


 protected void Page_Load(object sender, EventArgs e)
 {
     if (!IsPostBack)
     {
         int userId = Convert.ToInt32(Session["UserName"]);

         bool completed = IsQuizCompleted(userId);
         hfQuizCompleted.Value = completed ? "true" : "false";

         if (completed)
         {
             LoadSlides(); // needed for markup
             return;
         }


         var resumeInfo = GetResumeInfo(userId);

         hfResumeQuestionId.Value = resumeInfo.QuestionId ?? "";
         hfResumeModuleId.Value = resumeInfo.ModuleId ?? "";



         LoadSlides();
     }
 }
