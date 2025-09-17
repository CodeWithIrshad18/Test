await context.SaveChangesAsync();

TempData["SuccessMessage"] = "Data Saved Successfully!";
return RedirectToAction("Homepage", "Innovation");

@if (TempData["SuccessMessage"] != null)
{
    <script>
        Swal.fire({
            title: "@TempData["SuccessMessage"]",
            icon: "success",
            width: 600,
            padding: "3em",
            color: "#28a745",
            background: "#fff",
            backdrop: `rgba(0,0,123,0.4)`,
            timer: 3000
        });
    </script>
}


<script>
    document.getElementById("evaluationForm").addEventListener("submit", function (event) {
        let isValid = true;
        let rows = document.querySelectorAll("tbody tr.evaluation-row");

        rows.forEach(function (row) {
            // Check if a score is selected (bg-success means selected)
            let selectedScore = row.querySelector("td.clickable-td.bg-success");

            if (!selectedScore) {
                isValid = false;

                // Highlight row if no score chosen
                let scoreTds = row.querySelectorAll("td.clickable-td");
                scoreTds.forEach(function (td) {
                    td.style.backgroundColor = '#f27474';
                    td.style.color = 'white';
                });
            } else {
                // Reset style if valid
                let scoreTds = row.querySelectorAll("td.clickable-td");
                scoreTds.forEach(function (td) {
                    td.style.backgroundColor = '';
                    td.style.color = '';
                });
            }
        });

        if (!isValid) {
            alert("Please select a score for every parameter.");
            event.preventDefault(); // Stop submission if invalid
        }
        // ✅ If valid → form submits normally → controller saves → redirects to Homepage
        // ✅ Homepage will show Swal alert from TempData
    });
</script>







this is my logic 
[HttpPost]
public async Task<IActionResult> Innovation_Evaluation(IFormCollection form, Guid? id)
{
	if (HttpContext.Session.GetString("Session") == null)
	{
		return RedirectToAction("Login", "User");
	}

	var userPno = HttpContext.Session.GetString("Session");
	if (userPno != "159618" && userPno != "151514")
	{
		return View("AccessDenied");
	}

	if (!id.HasValue)
	{
		return BadRequest("ID is required");
	}


	var existingEvaluations = await context.AppEvaluationDetails
		.Where(e => e.MasterId == id.Value)
		.ToListAsync();


	var parameters = await context.AppParameters
		.FromSqlRaw("Select * From App_Parameters")
		.ToListAsync();


	foreach (var param in parameters)
	{
		var ParameterCode = param.ParameterCode;
		var selectedValue = form["HiddenScore_" + ParameterCode];



		if (!string.IsNullOrEmpty(selectedValue) && decimal.TryParse(selectedValue, out decimal selectedScore))
		{

			decimal weightage = param.Weightage ?? 0;
			decimal CalculatedScore = (selectedScore * weightage) / 100;


			var existingEvaluation = existingEvaluations
				.FirstOrDefault(e => e.ParameterCode == ParameterCode);

			if (existingEvaluation != null)
			{

				existingEvaluation.CreatedOn = DateTime.Now;
				existingEvaluation.CreatedBy = userPno;

				existingEvaluation.Sc2 = 0;
				existingEvaluation.Sc4 = 0;
				existingEvaluation.Sc6 = 0;
				existingEvaluation.Sc8 = 0;
				existingEvaluation.Sc10 = 0;


				switch (selectedScore)
				{
					case 20: existingEvaluation.Sc2 = 20; break;
					case 40: existingEvaluation.Sc4 = 40; break;
					case 60: existingEvaluation.Sc6 = 60; break;
					case 80: existingEvaluation.Sc8 = 80; break;
					case 100: existingEvaluation.Sc10 = 100; break;
				}

				existingEvaluation.Score = CalculatedScore;
			}
			else
			{

				bool isDuplicate = await context.AppEvaluationDetails
					.AnyAsync(e => e.ParameterCode == ParameterCode && e.MasterId == id.Value);

				if (!isDuplicate)
				{

					var newEvaluation = new AppEvaluationDetail
					{
						Id = Guid.NewGuid(),
						ParameterCode = ParameterCode,
						MasterId = id.Value,
						CreatedOn = DateTime.Now,
						CreatedBy = userPno,
						Score = CalculatedScore
					};

					switch (selectedScore)
					{
						case 20: newEvaluation.Sc2 = 20; break;
						case 40: newEvaluation.Sc4 = 40; break;
						case 60: newEvaluation.Sc6 = 60; break;
						case 80: newEvaluation.Sc8 = 80; break;
						case 100: newEvaluation.Sc10 = 100; break;
					}

					await context.AppEvaluationDetails.AddAsync(newEvaluation);
				}
			}
		}
	}

	await context.SaveChangesAsync();

	return RedirectToAction("Homepage", "Innovation");
}

and this is my js 
<script>


	document.getElementById("evaluationForm").addEventListener("submit", function (event) {
		let isValid = true;
		let rows = document.querySelectorAll("tbody tr.evaluation-row");

		rows.forEach(function (row) {
			// Check if the row has any 'clickable-td' td with class 'bg-success' (indicating a selected score)
			let selectedScore = row.querySelector("td.clickable-td.bg-success");

			// Check only for the columns representing the scores (not Weightage or Score columns)
			if (!selectedScore) {
				isValid = false;


				let scoreTds = row.querySelectorAll("td.clickable-td");
				scoreTds.forEach(function (td) {
					td.style.backgroundColor = '#f27474';
					td.style.color = 'white';
				});
			} else {

				let scoreTds = row.querySelectorAll("td.clickable-td");
				scoreTds.forEach(function (td) {
					td.style.backgroundColor = '';
					td.style.color = '';
				});
			}
		});

		if (!isValid) {

			alert("Please select a score for every parameter.");
			event.preventDefault();
		} else {

			Swal.fire({
				title: "Data Saved Successfully",
				width: 600,
				padding: "3em",
				color: "#28a745",
				background: "#fff",
				backdrop: `rgba(0,0,123,0.4)`,
				timer: 8000
			}).then(() => {
				this.submit();
			});
		}
	});




</script>

i want to show message after data is saved
