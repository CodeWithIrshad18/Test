this is my controller method , in this i want the condition that if it reaches limit to 20  hide the button and paused the video and not for the registered 20 users
public IActionResult UploadImage()
 {
     var pno = HttpContext.Request.Cookies["Session"];
     var userName = HttpContext.Request.Cookies["UserName"];

     ViewBag.Pno = pno;
     ViewBag.Name = userName;

     if (string.IsNullOrEmpty(pno) || string.IsNullOrEmpty(userName))
     {
         return RedirectToAction("Login", "User");
     }

     var PnoEnameList = context1.AppEmployeeMasters
             .Select(x => new
             {
                 Pno = x.Pno,
                 Ename = x.Ename,
             })
             .ToList();
     ViewBag.PnoEnameList = PnoEnameList;


   

     return View();
 }

like this 

                    var existingPerson = await context.AppPeople.FirstOrDefaultAsync(p => p.Pno == Pno);

                    if (existingPerson != null)
                    {
                       
                        existingPerson.Name = Name;
                        existingPerson.Image = fileName;
                        context.AppPeople.Update(existingPerson);
                    }

                    else
                    {
                        
                        int registeredCount = await context.AppPeople.Select(p => p.Pno).Distinct().CountAsync();

                        if (registeredCount >= 20)
                        {
                            
                            return BadRequest(new { success = false, message = "Upload limit reached to 20. Only existing users can update their image." });
                        }

                        
                        var person = new AppPerson
                        {
                            Pno = Pno,
                            Name = Name,
                            Image = fileName
                        };
                        context.AppPeople.Add(person);
                    }
