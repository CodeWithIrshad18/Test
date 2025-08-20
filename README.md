public IActionResult MyAction()
{
    var teams = context.Teams.ToList();

    var pnoSpecificKpis = teams
        .Where(t => !string.IsNullOrEmpty(t.Pno)) // ✅ skip null/empty Pno
        .GroupBy(t => t.Pno)
        .ToDictionary(
            group => group.Key,
            group => context.AppDareToTryMasters
                .Where(option => option.PersonalNumber == group.Key)
                .ToList()
        );

    ViewBag.PnoSpecificKpis = pnoSpecificKpis;

    // Get currentPno (your logic – e.g., from session, query, logged-in user)
    string currentPno = GetCurrentUserPno(); 

    if (string.IsNullOrEmpty(currentPno))
    {
        // 🔴 Log to console (development)
        Console.WriteLine("DEBUG: currentPno is NULL or empty.");

        // ✅ Or log using ILogger (recommended)
        //_logger.LogWarning("currentPno is NULL or empty for user {UserId}", User.Identity?.Name);
    }
    else if (!pnoSpecificKpis.ContainsKey(currentPno))
    {
        Console.WriteLine($"DEBUG: currentPno '{currentPno}' not found in PnoSpecificKpis.");
    }

    ViewBag.CurrentPno = currentPno;

    return View();
}



this is my logic how to handle this 

		@if (Model.appProjectTeams != null)
								{
									for (int i = 0; i < Model.appProjectTeams.Count; i++)
									{
										var currentPno = Model.appProjectTeams[i].Pno;
										var kpiOptions = ViewBag.PnoSpecificKpis[currentPno] as List<AppDareToTryMaster>;

										<div class="form-group row" data-index="@i">
											<div class="col-sm-3 d-flex align-items-center teamtext">
												<input asp-for="appProjectTeams[@i].Pno" class="form-control pno-input form-control-sm"
													   id="Pno" value="@Model.appProjectTeams[i].Pno"
													   name="appProjectTeams[@i].Pno"
													   placeholder="Max 6 digits" maxlength="6" autocomplete="off" />
											</div>
											<div class="col-sm-5 d-flex align-items-center teamtext">
												<input asp-for="appProjectTeams[@i].EmployeeName" type="text"
													   class="form-control form-control-sm name-input"
													   name="appProjectTeams[@i].EmployeeName"
													   value="@Model.appProjectTeams[i].EmployeeName" readonly />
											</div>
											<div class="col-sm-3 d-flex align-items-center teamtext">
												<input asp-for="appProjectTeams[@i].Contact" type="text"
													   class="form-control form-control-sm contact-input"
													   name="appProjectTeams[@i].Contact"
													   value="@Model.appProjectTeams[i].Contact" readonly />
											</div>
											<div class="col-sm-3 d-flex align-items-center teamtext">
												<label class="control-label">Dare To Try :</label>
											</div>
											<div class="col-sm-8" style="margin-top:5px;">
												<select asp-for="appProjectTeams[@i].DareToTry"
														class="form-control form-control-sm"
														id="daretotry-dropdown"
														name="appProjectTeams[@i].DareToTry" id="dare-to-try-dropdown">
													<option value="">Select Dare To Try</option>
													@if (kpiOptions != null)
													{
														foreach (var item in kpiOptions)
														{
															if (Model.appProjectTeams[i].DareToTry == item.Kpi)
															{
																<option value="@item.Kpi" selected>@item.Kpi</option>
															}
															else
															{
																<option value="@item.Kpi">@item.Kpi</option>
															}
														}
													}
												</select>

											</div>
											<div class="col-sm-1 d-flex align-items-center teambtn">
												@if (i >= 0)
												{
													<i class="fa fa-times btn btn-danger fa-lg remove-row2" aria-hidden="true"
													   style="color:#fff;cursor:pointer;" onclick="removeRow2(this)"></i>
												}
											</div>
											<input type="hidden" name="appProjectTeams[@i].Id" value="@Model.appProjectTeams[i].Id" />
										</div>

									}
								}
