
if (actionType == "Submit")
{
    if (!ModelState.IsValid)
        return BadRequest(ModelState);

    var duplicate = await context.AppEmpPositions
        .AnyAsync(x => x.Position == appPosition.Position
                    && x.Id != appPosition.Id);

    if (duplicate)
    {
        return Ok(new
        {
            status = "duplicate",
            message = "This position is already mapped to another employee."
        });
    }

    if (existingParameter != null)
    {
        context.Entry(existingParameter).CurrentValues.SetValues(appPosition);
        await context.SaveChangesAsync();
        return Ok(new { status = "success", message = "Updated" });
    }
    else
    {
        await context.AppEmpPositions.AddAsync(appPosition);
        await context.SaveChangesAsync();
        return Ok(new { status = "success", message = "Created" });
    }
}







if (ModelState.IsValid)
{
    var duplicate = await context.AppPositionWorksites
        .AnyAsync(x => x.Position == appPosition.Position
                    && x.Id != appPosition.Id);

    if (duplicate)
    {
        TempData["DuplicateMsg"] = "This position already exists!";
        return RedirectToAction("PositionMaster");
    }

    if (existingParameter != null)
    {
        context.Entry(existingParameter).CurrentValues.SetValues(appPosition);
        await context.SaveChangesAsync();
        TempData["Updatedmsg"] = "Position Updated Successfully!";
    }
    else
    {
        appPosition.CreatedBy = UserId;
        appPosition.CreatedOn = DateTime.Now;
        await context.AppPositionWorksites.AddAsync(appPosition);
        await context.SaveChangesAsync();
        TempData["msg"] = "Position Added Successfully!";
    }

    return RedirectToAction("PositionMaster");
}








these are my 2 tables and data
select * from App_Emp_Position where Pno = '151514'

ID	Pno	Position
1B6F3027-43A2-45F7-A8E5-3CE359DF81EB	151514	51111819



Post method for the upper query 
[HttpPost]
[ValidateAntiForgeryToken]
[PreventFlood(3)]
public async Task<IActionResult> EmployeePositionMaster([FromBody] AppEmpPosition appPosition, [FromQuery] string actionType)
{


    if (string.IsNullOrEmpty(actionType))
    {
        return BadRequest("No action specified.");
    }

    var existingParameter = await context.AppEmpPositions.FindAsync(appPosition.Id);

    var UserId = HttpContext.Request.Cookies["Session"];

    if (actionType == "Submit")
    {
        if (!ModelState.IsValid)
        {
            return BadRequest(ModelState);
        }

        var duplicate = await context.AppEmpPositions
              .Where(x => x.Position == appPosition.Position && x.Pno == appPosition.Pno)
              .Where(x => x.Id != appPosition.Id)
              .FirstOrDefaultAsync();

        if (duplicate != null)
        {
            return Ok(new { status = "duplicate", message = "Duplicate position exists for the same Personal No!" });
        }


        if (existingParameter != null)
        {
            context.Entry(existingParameter).CurrentValues.SetValues(appPosition);
            await context.SaveChangesAsync();
            return Ok(new { status = "success", message = "Updated" });

        }
        else
        {
            await context.AppEmpPositions.AddAsync(appPosition);
            await context.SaveChangesAsync();
            return Ok(new { status = "success", message = "Created" });
        }
    }
    else if (actionType == "Delete")
    {
        if (existingParameter != null)
        {
            context.AppEmpPositions.Remove(existingParameter);
            await context.SaveChangesAsync();
            return Ok("Deleted");
        }
    }

    return BadRequest("Invalid action.");
}


select * from App_Position_worksite where Position = '51111819'


ID	Position	Worksite	CreatedBy	CreatedOn
D688464C-5A02-481A-BE75-3D0D5BCA9593	51111819	428614DE-BCBB-4310-B13F-8080D973C6D2  151514	2025-11-05 14:01:55.537



controller code for upper query 

[HttpPost]
[ValidateAntiForgeryToken]
[PreventFlood(3)]
public async Task<IActionResult> PositionMaster(AppPositionWorksite appPosition, string actionType)
{
    if (string.IsNullOrEmpty(actionType))
    {
        return BadRequest("No action specified.");
    }

    var existingParameter = await context.AppPositionWorksites.FindAsync(appPosition.Id);
    var UserId = HttpContext.Request.Cookies["Session"];

    if (actionType == "Submit")
    {
        if (!ModelState.IsValid)
        {
            foreach (var state in ModelState)
            {
                foreach (var error in state.Value.Errors)
                {
                    Console.WriteLine($"Key:{state.Key},Error:{error.ErrorMessage}");
                }
            }
        }

        if (ModelState.IsValid)
        {

            var duplicate = await context.AppPositionWorksites
                .Where(x => x.Position == appPosition.Position)
                .Where(x => x.Id != appPosition.Id)
                .FirstOrDefaultAsync();

            if (duplicate != null)
            {
                TempData["DuplicateMsg"] = "Duplicate position exists for the same worksite!";
                return RedirectToAction("PositionMaster");
            }

            if (existingParameter != null)
            {
                appPosition.CreatedBy = UserId;
                appPosition.CreatedOn = DateTime.Now;
                context.Entry(existingParameter).CurrentValues.SetValues(appPosition);
                await context.SaveChangesAsync();
                TempData["Updatedmsg"] = "Position Updated Successfully!";
                return RedirectToAction("PositionMaster");
            }
            else
            {
                appPosition.CreatedBy = UserId;
                await context.AppPositionWorksites.AddAsync(appPosition);
                await context.SaveChangesAsync();
                TempData["msg"] = "Position Added Successfully!";
                return RedirectToAction("PositionMaster");
            }
        }
    }
    else if (actionType == "Delete")
    {
        if (existingParameter != null)
        {
            context.AppPositionWorksites.Remove(existingParameter);
            await context.SaveChangesAsync();
            TempData["Dltmsg"] = "Position Deleted Successfully!";
        }
    }

    return RedirectToAction("PositionMaster");
}

i dont want to store duplicate values . please check for both 
