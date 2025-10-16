
<div class="col-sm-3">
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

this is my jquery

<script>
    document.addEventListener('DOMContentLoaded', function () {
        const DropdownInput = document.getElementById('worksiteDropdown');
        const checkboxes = document.querySelectorAll('.worksite-checkbox');

        const hiddenInput = document.getElementById('Worksite');


        checkboxes.forEach(checkbox => {
            checkbox.addEventListener('change', () => {
                const selectedValues = Array.from(checkboxes).filter(cb => cb.checked).map(cb => cb.value);
                hiddenInput.value = selectedValues.join(',');
            });

        });

        function UpdateSelectedCount() {
            const selectedCount = Array.from(checkboxes).filter(checkbox => checkbox.checked).length;
            DropdownInput.value = `${selectedCount} selected`;
        }

        checkboxes.forEach(checkbox => {
            checkbox.addEventListener('change', UpdateSelectedCount);
        });


    });




</script>



<script>
    $(document).ready(function () {

     
        $('#showFormButton2').click(function () {
            $('#formContainer').show();
            $('#form2')[0].reset();  
            $('#form2 #Worksite').val('');
            $('#form2 #Position').val('');
            $('#form2 #worksiteDropdown').val('');
            $('#deleteButton').hide();
        });

      
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


                    var worksiteArray = response.worksite.split(',').map(x => x.trim().toLowerCase());



                    $("#worksiteDropdown").val(worksiteArray.length + ' selected');

                    $('.worksite-checkbox').each(function () {
    if (worksiteArray.includes($(this).val().toLowerCase())) {
        $(this).prop('checked', true);
    } else {
        $(this).prop('checked', false);
    }
});

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
