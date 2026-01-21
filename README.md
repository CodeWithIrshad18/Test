SELECT 
    amm.ID,
    amm.ModuleName,
    CASE 
        -- 1️⃣ No questions at all
        WHEN NOT EXISTS (
            SELECT 1
            FROM App_QuestionMaster q
            WHERE q.ModuleID = amm.ID
              AND q.IsActive = 1
        )
        THEN 'No Questions'

        -- 2️⃣ Questions exist AND all answered
        WHEN NOT EXISTS (
            SELECT 1
            FROM App_QuestionMaster q
            WHERE q.ModuleID = amm.ID
              AND q.IsActive = 1
              AND NOT EXISTS (
                    SELECT 1
                    FROM ASP_User_Response r
                    WHERE r.QuestionID = q.Id
                      AND r.UserID = @UserId
              )
        )
        THEN 'Done'

        -- 3️⃣ Questions exist but not fully answered
        ELSE 'Not Done'
    END AS module_status
FROM App_Module_Master amm
ORDER BY amm.SerialNo ASC;






Must declare the scalar variable "@UserId".



Line 42:             objParam.Add("@UserId", userId);
Line 43: 
Line 44:             return dh.GetDataset(strSQL, "App_Quiz_Result", objParam);
Line 45:         }



 public DataSet GetRecordsFOrResult(string userId)
 {
     string strSQL = @"
 SELECT 
     amm.ID,
     amm.ModuleName,
     CASE 
         WHEN NOT EXISTS (
             SELECT 1
             FROM App_QuestionMaster q
             WHERE q.ModuleID = amm.ID
               AND q.IsActive = 1
               AND NOT EXISTS (
                     SELECT 1
                     FROM ASP_User_Response r
                     WHERE r.QuestionID = q.Id
                       AND r.UserID = @UserId
               )
         )
         THEN 'Done'
         ELSE 'Not Done'
     END AS module_status
 FROM App_Module_Master amm
 ORDER BY amm.SerialNo ASC";

     DataHelper dh = new DataHelper();
     Dictionary<string, object> objParam = new Dictionary<string, object>();
     objParam.Add("@UserId", userId);

     return dh.GetDataset(strSQL, "App_Quiz_Result", objParam);
 }
