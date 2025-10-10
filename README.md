previously I have used this 

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

I want like this 
