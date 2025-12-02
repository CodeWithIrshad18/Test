<script>

function containsHtml(s) {
    return /<[^>]+>/g.test(s);
}

$(document).ready(function () {

    // Read Anti-Forgery Token
    function getToken() {
        return $('input[name="__RequestVerificationToken"]').val();
    }

    // Validate fields
    function validateForm() {
        let isValid = true;
        $('.is-invalid').removeClass('is-invalid');

        if ($('#WorkSite').val().trim() === '') {
            $('#WorkSite').addClass('is-invalid');
            isValid = false;
        }

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

    // Show form
    $('#showFormButton2').click(function () {
        $('#formContainer').show();
        $('#form2')[0].reset();
        $('#deleteButton').hide();
        $('#addRowButton').show();
    });

    // Edit form load
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
                $('#LocationId').val(response.id);
                $('#CreatedBy').val(response.createdby);
                $('#CreatedOn').val(response.createdon);
                $('#WorkSite').val(response.worksite);
                $('#Longitude').val(response.longitude);
                $('#Latitude').val(response.latitude);
                $('#Range').val(response.range);

                $('#formContainer').show();
            },
            error: function () {
                alert("Unable to load form data.");
            }
        });
    });

    // Add row
    $('#addRowButton').click(function () {
        const newRow =
            `<div class="location-row">
                <div class="row mt-2">
                    <div class="col-sm-5">
                        <input name="Latitude[]" class="form-control form-control-sm LatitudeInput" placeholder="Enter Latitude" autocomplete="off"/>
                    </div>
                    <div class="col-sm-5">
                        <input name="Longitude[]" class="form-control form-control-sm LongInput" placeholder="Enter Longitude" autocomplete="off"/>
                    </div>
                    <div class="col-sm-2">
                        <button type="button" class="btn btn-danger btn-sm remove-row">Remove</button>
                    </div>
                </div>
            </div>`;

        $('#locationContainer').append(newRow);
    });

    // Remove row
    $(document).on('click', '.remove-row', function () {
        $(this).closest('.location-row').remove();
    });


    // ---------------------------
    //  SAVE / SUBMIT
    // ---------------------------
    $('#submitButton').click(function (e) {
        e.preventDefault();

        if (!validateForm()) {
            Swal.fire("Validation Error", "Please fill all fields", "warning");
            return;
        }

        // HTML Validation
        let htmlInvalid = false;
        ['#WorkSite', '#Range'].forEach(id => {
            if (containsHtml($(id).val())) htmlInvalid = true;
        });

        $('.LatitudeInput, .LongInput').each(function () {
            if (containsHtml($(this).val())) htmlInvalid = true;
        });

        if (htmlInvalid) {
            Swal.fire("Validation Error", "HTML Tags Not Allowed", "warning");
            return;
        }

        const id = $('#LocationId').val();
        const worksite = $('#WorkSite').val();
        const range = $('#Range').val();

        const rowsData = [];
        $('.location-row').each(function () {
            rowsData.push({
                WorkSite: worksite,
                Range: range,
                Latitude: parseFloat($(this).find('.LatitudeInput').val()),
                Longitude: parseFloat($(this).find('.LongInput').val()),
                Id: id
            });
        });

        $.ajax({
            url: '@Url.Action("LocationMaster", "Master")',
            type: 'POST',
            headers: {
                "RequestVerificationToken": getToken()
            },
            contentType: 'application/json',
            data: JSON.stringify({
                Id: id,
                AppLocations: rowsData,
                ActionType: "Submit"
            }),
            success: function () {
                Swal.fire("Success", "Location saved successfully.", "success")
                    .then(() => location.reload());
            },
            error: function (xhr) {
                console.log(xhr.responseText);
                Swal.fire("Error", "Bad Request - CSRF validation failed.", "error");
            }
        });
    });


    // ---------------------------
    //  DELETE
    // ---------------------------
    $('#deleteButton').click(function (e) {
        e.preventDefault();

        const id = $('#LocationId').val();

        $.ajax({
            url: '@Url.Action("LocationMaster", "Master")',
            type: 'POST',
            headers: {
                "RequestVerificationToken": getToken()
            },
            contentType: 'application/json',
            data: JSON.stringify({
                Id: id,
                AppLocations: [{ Id: id }],
                ActionType: "Delete"
            }),
            success: function () {
                Swal.fire("Deleted", "The location has been deleted.", "success")
                    .then(() => location.reload());
            },
            error: function (xhr) {
                console.log(xhr.responseText);
                Swal.fire("Error", "Delete failed - AntiForgery validation error.", "error");
            }
        });
    });

});
</script>





<script>
    
function containsHtml(s) {
    return /<[^>]+>/g.test(s);
}

    </script>
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


                        if (containsHtml($('#WorkSite').val())) {
     Swal.fire({
                    title: 'Validation Error',
                    text: 'HTML tags not allowed in Worksite',
                    icon: 'warning',
                    confirmButtonColor: '#3085d6'
                });
                return;
}

 if (containsHtml($('#Range').val())) {
     Swal.fire({
                    title: 'Validation Error',
                    text: 'HTML tags not allowed in Worksite',
                    icon: 'warning',
                    confirmButtonColor: '#3085d6'
                });
                return;
}

if (containsHtml($('#Longitude').val())) {
     Swal.fire({
                    title: 'Validation Error',
                    text: 'HTML tags not allowed in Worksite',
                    icon: 'warning',
                    confirmButtonColor: '#3085d6'
                });
                return;
}
if (containsHtml($('#Latitude').val())) {
     Swal.fire({
                    title: 'Validation Error',
                    text: 'HTML tags not allowed in Worksite',
                    icon: 'warning',
                    confirmButtonColor: '#3085d6'
                });
                return;
}

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

error : 

jquery.min.js:2 
 POST https://localhost:7153/Master/LocationMaster 400 (Bad Request)
