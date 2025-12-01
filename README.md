refNoLink error :

Uncaught TypeError: Cannot set properties of null (setting 'value')
    at updateSelectedSources (FeederMaster:442:49)
    at loadExistingSources (FeederMaster:429:5)
    at HTMLAnchorElement.<anonymous> (FeederMaster:375:17)

NewButton error : 

FeederMaster:352 Uncaught TypeError: Cannot set properties of null (setting 'value')
    at HTMLButtonElement.<anonymous> (FeederMaster:352:31)

﻿Dropdown : 

                <div class="col-md-1">
        <label for="SourceName" class="control-label">Source Name</label>
    </div>
    <div class="col-md-3">
        <div class="dropdown">
            <input class="dropdown-toggle form-control form-control-sm custom-select text-start" type="button"
                   id="SourceDropdown" data-bs-toggle="dropdown" aria-expanded="false"/>
            <ul class="dropdown-menu w-100" aria-labelledby="SourceDropdown" id="SourceList">
               @if (sources != null && sources.Any())
{
    foreach (var item in sources)
    {
        <li style="margin-left:5%;">
            <div class="form-check">
                <input type="checkbox" class="form-check-input Source-checkbox"
                       value="@item.ID" id="div_@item.ID" />
                <label class="form-check-label" for="div_@item.ID">
                    @item.SourceName
                </label>
            </div>
        </li>
    }
}
else
{
    <li class="text-danger ms-2">No Source Found</li>
}

            </ul>
        </div>
        <input type="hidden" id="SourceID" name="SourceID" />
    </div>

Js :

<script>

document.addEventListener("DOMContentLoaded", function () {

    var newButton = document.getElementById("newButton");
    var SourceMaster = document.getElementById("form");
    var refNoLinks = document.querySelectorAll(".refNoLink");
    var deleteButton = document.getElementById("deleteButton");
    var submitButton = document.getElementById("submitButton");
    var actionTypeInput = document.getElementById("actionType");

    const checkboxes = document.querySelectorAll(".Source-checkbox");
    const hiddenInput = document.getElementById("SourceName");
    const dropdownBtn = document.getElementById("SourceDropdown");


    if (newButton) {
        newButton.addEventListener("click", function () {
            SourceMaster.style.display = "block";

            document.getElementById("SourceID").value = "";
            document.getElementById("FeederName").value = "";
            hiddenInput.value = "";


            checkboxes.forEach(cb => cb.checked = false);


            dropdownBtn.value = "Select Source";

            deleteButton.style.display = "none";
        });
    }

    refNoLinks.forEach(link => {
        link.addEventListener("click", function (event) {
            event.preventDefault();
            SourceMaster.style.display = "block";

            document.getElementById("FeederName").value = this.getAttribute("data-FeederName");
            document.getElementById("SourceID").value = this.getAttribute("data-SourceID");
            document.getElementById("FeederId").value = this.getAttribute("data-id");

            let existingSourceIds = this.getAttribute("data-sourceid");
            if (existingSourceIds) {
                loadExistingSources(existingSourceIds);
            }

            if (deleteButton) {
                deleteButton.style.display = "inline-block";
            }
        });
    });


    submitButton.addEventListener("click", function () {
        actionTypeInput.value = "save";
    });


    if (deleteButton) {
        deleteButton.addEventListener("click", function () {
            Swal.fire({
                title: 'Are you sure?',
                text: "Do you really want to delete this Source?",
                icon: 'warning',
                showCancelButton: true,
                confirmButtonColor: '#3085d6',
                cancelButtonColor: '#d33',
                confirmButtonText: 'Yes, delete it!',
                cancelButtonText: 'Cancel'
            }).then((result) => {
                if (result.isConfirmed) {
                    actionTypeInput.value = "delete";
                    document.getElementById("form").submit();
                }
            });
        });
    }


    checkboxes.forEach(cb => {
        cb.addEventListener("change", updateSelectedSources);
    });

});


function loadExistingSources(sourceIds) {
    let ids = sourceIds.split(",");


    document.querySelectorAll(".Source-checkbox").forEach(cb => cb.checked = false);

    ids.forEach(id => {
        let checkbox = document.querySelector('.Source-checkbox[value="' + id + '"]');
        if (checkbox) checkbox.checked = true;
    });

    updateSelectedSources();
}


function updateSelectedSources() {
    let selectedIds = [];

    document.querySelectorAll(".Source-checkbox").forEach(cb => {
        if (cb.checked) {
            selectedIds.push(cb.value);
        }
    });

    document.getElementById("SourceName").value = selectedIds.join(",");


    let btn = document.getElementById("SourceDropdown");
    btn.value = selectedIds.length > 0 
        ? selectedIds.length + " selected"
        : "Select Source";
}

</script>
