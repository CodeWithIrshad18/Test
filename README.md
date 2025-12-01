  [HttpPost]
  public async Task<IActionResult> LocationMaster([FromBody] LocationMasterViewModel model, Guid? Id)
  {


      var UserId = HttpContext.Request.Cookies["Session"];
      if (model == null || model.appLocations == null || !model.appLocations.Any())
      {
          return BadRequest("No location data received.");
      }

      

      if (string.IsNullOrEmpty(model.actionType))
      {
          return BadRequest("No action specified.");
      }

      if (model.actionType == "Submit")
      {
          if (!ModelState.IsValid)
          {
              foreach (var state in ModelState)
              {
                  foreach (var error in state.Value.Errors)
                  {
                      Console.WriteLine($"Key:{state.Key}, Error:{error.ErrorMessage}");
                  }
              }
          }

          foreach (var appLocation in model.appLocations)
          {

              if (ContainsHtml(appLocation.WorkSite))
              {
                  ModelState.AddModelError("Name", "HTML tags are not allowed.");
                  return View(model);
              }

              if (ContainsHtml(appLocation.Range))
              {
                  ModelState.AddModelError("Name", "HTML tags are not allowed.");
                  return View(model);
              }
              if (ContainsHtml(appLocation.Latitude.ToString()))
              {
                  ModelState.AddModelError("Name", "HTML tags are not allowed.");
                  return View(model);
              }
              if (ContainsHtml(appLocation.Longitude.ToString()))
              {
                  ModelState.AddModelError("Name", "HTML tags are not allowed.");
                  return View(model);
              }


              var existingLocation = await context.AppLocationMasters.FindAsync(appLocation.Id);

             

              appLocation.CreatedBy = UserId;
              if (existingLocation != null)
              {


                  context.Entry(existingLocation).CurrentValues.SetValues(appLocation);

              }
              else
              {

                  await context.AppLocationMasters.AddAsync(appLocation);
              }
          }


          await context.SaveChangesAsync();


          TempData["msg"] = "Location Saved Successfully!";
          return RedirectToAction("LocationMaster");
      }
      else if (model.actionType == "Delete")
      {
          foreach (var appLocation in model.appLocations)
          {

              var existingLocation = await context.AppLocationMasters.FindAsync(appLocation.Id);
              if (existingLocation != null)
              {

                  context.AppLocationMasters.Remove(existingLocation);
              }
          }


          await context.SaveChangesAsync();


          TempData["Dltmsg"] = "Location Deleted Successfully!";
          return RedirectToAction("LocationMaster");
      }

      return View(model);
  }

<div id="formContainer" style="display:none;">
    <form asp-action="LocationMaster" asp-controller="Master" id="form2" method="post">
        @Html.AntiForgeryToken()
        <div asp-validation-summary="ModelOnly" class="text-danger"></div>
        <div class="card rounded-9">
            <div class="card-header text-center" style="background-color:rgb(210 177 255);font-weight: 800;">Location Master</div>

            <div class="col-md-12">
                <fieldset style="border:1px solid #bfbebe;padding:5px 20px 5px 20px;border-radius:6px;">
                    <div class="row">

                        <div class="form-group row">

                            <div class="col-sm-1 d-flex">
                                <label asp-for="WorkSite" class="control-label">Worksite</label>
                            </div>
                            <div class="col-sm-3">
                                <input asp-for="WorkSite" class="form-control form-control-sm WorkSiteInput" id="WorkSite" placeholder="" required autocomplete="off" />

                            </div>
                            <div class="col-sm-1 d-flex">
                                <label asp-for="Range" class="control-label">Range</label>
                            </div>
                            <div class="col-sm-2">
                                <input asp-for="Range" class="form-control form-control-sm rangeInput" id="Range" placeholder="" required autocomplete="off" />

                            </div>
                            <div class="col-md-5">
                                <div class="card rounded-j">
                                    <div class="row">
                                        <div class="col-sm-5 d-flex align-items-center teamtext">
                                            <label class="control-label">Latitude</label>
                                        </div>
                                        <div class="col-sm-4 d-flex align-items-center teamtext">
                                            <label class="control-label">Longitude</label>
                                        </div>
                                    </div>
                                    <div id="locationContainer">
                                        <div class="location-row">
                                            <div class="row mt-2">
                                                <div class="col-sm-5 d-flex align-items-center">
                                                    <input asp-for="Latitude" class="form-control form-control-sm LatitudeInput" id="Latitude" placeholder="Enter Latitude" required autocomplete="off" />
                                                </div>
                                                <div class="col-sm-5 d-flex align-items-center">
                                                    <input asp-for="Latitude" name="Longitude[]" class="form-control form-control-sm LongInput" id="Longitude" placeholder="Enter Longitude" required autocomplete="off" />
                                                </div>
                                            </div>

                                        </div>
                                    </div>
                                </div>
                            </div>

                        </div>






                        <input type="hidden" name="Id" value="@Model.Id" />
                        <div class="form-group row mt-2">

                            <input asp-for="Id" type="text" value="@Model.Id" id="LocationId" hidden />
                            <input name="CreatedOn" value="@Model.CreatedOn" hidden id="CreatedOn" />
                            <input name="CreatedBy" value="@ViewBag.CreatedBy" hidden id="CreatedBy" />
                        </div>
                        <input type="hidden" name="actionType" id="actionType" value="" />
                        <div class="form-group row">
                            <div class="col-sm-12 text-center">
                                <!-- Submit Button -->
                                <button type="button" id="submitButton" name="actionType" class="btn btn-primary">Submit</button>
                                <button type="button" id="deleteButton" name="actionType" class="btn btn-danger">Delete</button>

                            </div>
                        </div>






                    </div>
                </fieldset>

            </div>
        </div>
    </form>


<script>
    $(document).ready(function () {

        function validateForm() {
            let isValid = true;

            // Remove existing validation styles
            $('.is-invalid').removeClass('is-invalid');

            // Check Worksite field
            if ($('#WorkSite').val().trim() === '') {
                $('#WorkSite').addClass('is-invalid');
                isValid = false;
            }

            // Check all Latitude and Longitude fields
            $('.location-row').each(function () {
                let latitude = $(this).find('.LatitudeInput');
                let longitude = $(this).find('.LongInput');

                if (latitude.val().trim() === '') {
                    latitude.addClass('is-invalid');
                    isValid = false;
                }

                if (longitude.val().trim() === '') {
                    longitude.addClass('is-invalid');
                    isValid = false;
                }
            });

            return isValid;
        }

        // Function to set the actionType before form submission
        function setAction(actionType, event = null) {
            if (event) event.preventDefault();
            $('#actionType').val(actionType);
            $('#form2').submit();
        }

        // Show the form for adding a new entry
        $('#showFormButton2').click(function () {
            $('#formContainer').show();
            $('#form2')[0].reset(); // Clear form fields
            $('#deleteButton').hide();
            $('#addRowButton').show();
        });

        // Open filled form for editing
        $(".OpenFilledForm").click(function (e) {
            e.preventDefault();
            $('#deleteButton').show();
            $('#addRowButton').hide();

            var id = $(this).data("id");
            $.ajax({
                url: '@Url.Action("LocationMaster", "Master")',
                type: 'GET',
                data: { id: id },
                success: function (response) {
                    // Populate form fields with response data
                    $('#form2 #LocationId').val(response.id);
                    $('#form2 #CreatedBy').val(response.createdby);
                    $('#form2 #CreatedOn').val(response.createdon);
                    $('#form2 #WorkSite').val(response.worksite);
                    $('#form2 #Longitude').val(response.longitude);
                    $('#form2 #Latitude').val(response.latitude);
                    $('#form2 #Range').val(response.range);


                    // Show the form
                    $('#formContainer').show();
                },
                error: function () {
                    alert("An error occurred while loading the form data.");
                }
            });
        });

        // Add a new row dynamically
        $('#addRowButton').click(function () {
            const newRow = `
                                    <div class="location-row">
                                        <div class="row mt-2">
                                            <div class="col-sm-5 d-flex align-items-center">
                                                        <input asp-for="Latitude" name="Latitude[]" class="form-control form-control-sm LatitudeInput" placeholder="Enter Latitude" required autocomplete="off"/>
                                            </div>
                                            <div class="col-sm-5 d-flex align-items-center">
                                                        <input asp-for="Longitude" name="Longitude[]" class="form-control form-control-sm LongInput" placeholder="Enter Longitude" required autocomplete="off"/>
                                            </div>
                                            <div class="col-sm-2 d-flex align-items-center">
                                                <button type="button" class="btn btn-danger btn-sm remove-row">Remove</button>
                                            </div>
                                        </div>
                                    </div>`;
            $('#locationContainer').append(newRow);
        });

        // Remove a row dynamically
        $(document).on('click', '.remove-row', function () {
            $(this).closest('.location-row').remove();
        });

        // Handle the submit button click
        $('#submitButton').click(function (e) {
            e.preventDefault();

            // Validate form fields
            if (!validateForm()) {
                Swal.fire({
                    title: 'Validation Error',
                    text: 'Please fill in all required fields.',
                    icon: 'warning',
                    confirmButtonColor: '#3085d6'
                });
                return;
            }

            const id = $('#LocationId').val();
            const rowsData = [];
            $('.location-row').each(function () {
                const latitude = $(this).find('.LatitudeInput').val();
                const longitude = $(this).find('.LongInput').val();
                const worksite = $('#WorkSite').val();
                const range = $('#Range').val();

                rowsData.push({
                    WorkSite: worksite,
                    Range: range,
                    Latitude: parseFloat(latitude),
                    Longitude: parseFloat(longitude),
                    Id: id
                });
            });

            $.ajax({
                url: '@Url.Action("LocationMaster", "Master")',
                type: 'POST',
                contentType: 'application/json',
                data: JSON.stringify({
                    Id: id,
                    appLocations: rowsData,
                    actionType: "Submit"
                }),
                success: function (response) {
                    Swal.fire({
                        title: 'Success!',
                        text: 'Location saved successfully.',
                        icon: 'success',
                        confirmButtonColor: '#3085d6'
                    }).then(() => {
                        $('#formContainer').hide();
                        location.reload();
                    });
                },
                error: function () {
                    alert('An error occurred while saving the locations.');
                }
            });
        });



        // Handle the delete button click
        $('#deleteButton').click(function (e) {
            e.preventDefault();
            const id = $('#LocationId').val();
            const rowsData = [];
            $('.location-row').each(function () {
                //const id = $(this).data('id');
                rowsData.push({ Id: id });
            });

            $.ajax({
                url: '@Url.Action("LocationMaster", "Master")',
                type: 'POST',
                contentType: 'application/json',
                data: JSON.stringify({ Id: id, appLocations: rowsData, actionType: "Delete" }),
                success: function (response) {
                    Swal.fire({
                        title: 'Deleted!',
                        text: 'The position has been deleted.',
                        icon: 'success',
                        confirmButtonColor: '#3085d6'
                    }).then(() => {
                        $('#formContainer').hide();
                        location.reload();
                    });
                },
                error: function () {
                    alert('An error occurred while deleting the locations.');
                }
            });
        });
    });
</script>
