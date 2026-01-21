SELECT 
    amm.ID,
    amm.ModuleName,
    CASE 
        WHEN 
            EXISTS (
                SELECT 1
                FROM App_QuestionMaster q
                WHERE q.ModuleID = amm.ID
                  AND q.IsActive = 1
            )
            AND NOT EXISTS (
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
ORDER BY amm.SerialNo ASC;





Module Name	Status
Home Page	Not Done
CHRO Message	Not Done
Instructions	Not Done
Milestone1	Not Done
Milestone3	Done
Milestone4	Not Done
