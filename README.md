SELECT 
    i.*,
    (SELECT DivisionName 
     FROM UserLoginDB.dbo.App_EmployeeMaster 
     WHERE Pno COLLATE DATABASE_DEFAULT = i.Personal_No COLLATE DATABASE_DEFAULT
    ) AS Division,
    (SELECT COUNT(*) 
     FROM App_Like 
     WHERE MasterId = i.Id AND Likes = 1
    ) AS TotalLikes,
    (SELECT COUNT(*) 
     FROM App_Comments 
     WHERE MasterId = i.Id
    ) AS TotalComments,
    (SELECT ISNULL(SUM(Score),0) 
     FROM App_Evaluation_Details 
     WHERE MasterId = i.Id
    ) AS TotalScore
FROM App_Innovation i
WHERE Status != 'Draft' 
ORDER BY CreatedOn;



i have this query 

SELECT i.*,(select DivisionName from UserLoginDB.dbo.App_EmployeeMaster where Pno COLLATE 
DATABASE_DEFAULT = i.Personal_No COLLATE DATABASE_DEFAULT) as Division
,(SELECT COUNT(*) FROM App_Like WHERE MasterId = i.Id AND Likes = 1)
AS TotalLikes,
(SELECT COUNT(*) FROM App_Comments WHERE MasterId = i.Id) AS TotalComments  FROM App_Innovation i 
WHERE Status != 'Draft' and 1=1 ORDER BY CreatedOn


and i want to fetch score from this table App_Evaluation_Details
