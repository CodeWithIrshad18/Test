  [HttpGet]
  public IActionResult GetDivisions()
  {
      using (var connection = new SqlConnection(GetConnection()))
      {
          string query = @"
          SELECT DISTINCT ema_exec_head_desc 
          FROM SAPHRDB.dbo.T_Empl_All
          WHERE ema_exec_head_desc IS NOT NULL
          ORDER BY ema_exec_head_desc";

          var divisions = connection.Query<Division>(query).ToList();
          return Json(divisions);
      }
  }

     
  [HttpGet]
  public IActionResult GetDepartments(string division)
  {
      if (string.IsNullOrEmpty(division))
          return Json(new List<Department>());

      using (var connection = new SqlConnection(GetConnection()))
      {
          string query = @"
          SELECT DISTINCT ema_exec_head_desc, ema_dept_desc
          FROM SAPHRDB.dbo.T_Empl_All
          WHERE ema_exec_head_desc = @division
                AND ema_dept_desc IS NOT NULL
          ORDER BY ema_dept_desc";

          var departments = connection.Query<Department>(query, new { division }).ToList();
          return Json(departments);
      }
  }

 
  [HttpGet]
  public IActionResult GetSections(string division, string department)
  {
      if (string.IsNullOrEmpty(division) || string.IsNullOrEmpty(department))
          return Json(new List<Section>());

      using (var connection = new SqlConnection(GetConnection()))
      {
          string query = @"
          SELECT DISTINCT ema_exec_head_desc, ema_dept_desc, ema_section_desc
          FROM SAPHRDB.dbo.T_Empl_All
          WHERE ema_exec_head_desc = @division
                AND ema_dept_desc = @department
                AND ema_section_desc IS NOT NULL
          ORDER BY ema_section_desc";

          var sections = connection.Query<Section>(query, new { division, department }).ToList();
          return Json(sections);
      }
  }
