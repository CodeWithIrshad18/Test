var worksiteArray = response.worksite.split(',').map(x => x.trim().toLowerCase());

$('.worksite-checkbox').each(function () {
    if (worksiteArray.includes($(this).val().toLowerCase())) {
        $(this).prop('checked', true);
    } else {
        $(this).prop('checked', false);
    }
});

$('#Worksite').val(worksiteArray.join(','));



this is my jquery
<script>
    $(document).ready(function () {

     
        $('#showFormButton2').click(function () {
            $('#formContainer').show();
            $('#form2')[0].reset();  // Clear the form fields
            $('#form2 #Worksite').val('');
            $('#form2 #Position').val('');
            $('#form2 #worksiteDropdown').val('');
            $('#deleteButton').hide();
        });

        // Open filled form for updating
        $(".OpenFilledForm").click(function (e) {
            e.preventDefault();
            $('#deleteButton').show();


            var id = $(this).data("id");

            $.ajax({
                url: '@Url.Action("PositionMaster", "Master")',
                type: 'GET',
                data: { id: id },
                success: function (response) {

                    $('#form2 #id').val(response.id);
                    $('#form2 #Positionid').val(response.id);
                    $('#form2 #Position').val(response.position);
                    $('#form2 #Worksite').val(response.worksite);
                    $('#form2 #CreatedBy').val(response.createdby);
                    $('#form2 #CreatedOn').val(response.createdon);


                    var worksiteArray = response.worksite.split(',');



                    $("#worksiteDropdown").val(worksiteArray.length + ' selected');

                    $('.worksite-checkbox').each(function () {
                        if (worksiteArray.includes($(this).val())) {
                            $(this).prop('checked', true);
                        } else {
                            $(this).prop('checked', false);
                        }
                    });


                    // Show the form
                    $('#formContainer').show();

                    $('#deletedId').val(response.id);
                },
                error: function () {
                    alert("An error occurred while loading the form data.");
                }
            });
        });
    });

</script>

this is my dropdown 

    <div class="dropdown">
        <input class="dropdown-toggle form-control form-control-sm custom-select" placeholder="" type="button"
               id="worksiteDropdown" data-bs-toggle="dropdown" aria-expanded="false" />



        <ul class="dropdown-menu w-100" aria-labelledby="worksiteDropdown" id="locationList">
            @foreach (var item in WorksiteDropdown)
            {
                <li style="margin-left:5%;">
                    <div class="form-check">
                        <input type="checkbox" class="form-check-input worksite-checkbox"
                               value="@item.Value" id="worksite_@item.Value" />
                        <label class="form-check-label" for="worksite_@item.Value">@item.Text</label>
                    </div>

                </li>
            }
        </ul>

    </div>
    <input type="hidden" id="Worksite" name="Worksite" />



</div>

in the checkbox some values are not checked but some checked 
