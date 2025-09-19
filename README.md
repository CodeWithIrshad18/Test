SELECT 
    ep.Pno,
    e.Ema_Ename,
    ep.Position,
    pw.CreatedBy,
    pw.CreatedOn,
    STRING_AGG(sites.Work_Site, ', ') AS Worksites
FROM SAPHRDB.dbo.T_Empl_All e
INNER JOIN App_Emp_Position ep ON e.Ema_perno = ep.Pno
INNER JOIN App_Position_Worksite pw ON ep.Position = pw.Position
OUTER APPLY (
    SELECT l.Work_Site
    FROM STRING_SPLIT(pw.Worksite, ',') AS split
    JOIN App_LocationMaster l ON TRY_CAST(split.value AS uniqueidentifier) = l.ID
) AS sites
WHERE (ema_pyrl_area = 'ZZ' OR ema_pyrl_area = 'JS')
  AND ema_dept_desc IN (SELECT value FROM STRING_SPLIT(@DeptName, ';'))

  -- 🔎 filter by search string (Pno or Name)
  AND (
       @SearchString = '' 
       OR ep.Pno LIKE '%' + @SearchString + '%'
       OR e.Ema_Ename LIKE '%' + @SearchString + '%'
  )

  -- 🔎 filter by PositionId
  AND (
       @PositionId = '' 
       OR ep.Position LIKE '%' + @PositionId + '%'
  )

  -- 🔎 filter by WorksiteId (CSV column LIKE)
  AND (
       @WorksiteId = '' 
       OR pw.Worksite LIKE '%' + @WorksiteId + '%'
  )
GROUP BY ep.Pno, e.Ema_Ename, ep.Position, pw.CreatedBy, pw.CreatedOn
ORDER BY ep.Pno;

string dataSql = @" ... (above query) ... ";

empList = (await conn.QueryAsync(dataSql, new
{
    DeptName = department,
    SearchString = searchString ?? "",
    PositionId = PositionId ?? "",
    WorksiteId = WorksiteName ?? ""
})).ToList();






i have like this data in my db to 
filter the worksite , i want to use like operator because there is multiple IDs there
428614DE-BCBB-4310-B13F-8080D973C6D2,D5397476-DDA4-466C-8C0E-67BC57CF83B9,4E4E8F72-8007-44FD-90A0-B90D2D9DDC49,AB83E78B-D033-49CD-912A-5A703750378E,DC15F77C-F813-4CDD-856D-CDCBACB306AE,34D7C031-E6E7-44C3-BB4B-F5A667A93B16

