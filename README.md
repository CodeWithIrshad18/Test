document.addEventListener("DOMContentLoaded", function () {

    const checkboxes = document.querySelectorAll(".Source-checkbox");
    const hiddenInput = document.getElementById("SourceName");
    const dropdownBtn = document.getElementById("SourceDropdown");

    // -----------------------------
    // UPDATE DROPDOWN TEXT + HIDDEN
    // -----------------------------
    function updateSelectedSources() {
        let selectedIds = [];
        let selectedNames = [];

        checkboxes.forEach(cb => {
            if (cb.checked) {
                selectedIds.push(cb.value);
                selectedNames.push(cb.nextElementSibling.innerText.trim());
            }
        });

        hiddenInput.value = selectedIds.join(",");

        // Show count instead of names
        dropdownBtn.value = selectedIds.length > 0
            ? `${selectedIds.length} selected`
            : "Select Source";
    }

    // ----------------------------------
    // LOAD EXISTING CHECKBOXES ON EDIT
    // ----------------------------------
    function loadExistingSources(sourceIds) {
        // Clear first
        checkboxes.forEach(cb => cb.checked = false);

        // Check existing
        if (sourceIds) {
            let ids = sourceIds.split(",");
            ids.forEach(id => {
                let checkbox = document.querySelector(`.Source-checkbox[value="${id}"]`);
                if (checkbox) checkbox.checked = true;
            });
        }

        // Update text + hidden field
        updateSelectedSources();
    }


    // -----------------------------------------
    // REF-NO-LINK CLICK (EDIT MODE)
    // -----------------------------------------
    document.querySelectorAll(".refNoLink").forEach(link => {
        link.addEventListener("click", function (event) {
            event.preventDefault();

            // Show your form
            document.getElementById("form").style.display = "block";

            // Load existing IDs in dropdown
            let existingIds = this.getAttribute("data-sourceid");
            loadExistingSources(existingIds);
        });
    });


    // -----------------------------------------
    // NEW BUTTON CLICK — CLEAR EVERYTHING
    // -----------------------------------------
    const newButton = document.getElementById("newButton");
    if (newButton) {
        newButton.addEventListener("click", function () {

            // Clear checkboxes
            checkboxes.forEach(cb => cb.checked = false);

            // Reset dropdown
            dropdownBtn.value = "Select Source";

            // Clear hidden field
            hiddenInput.value = "";
        });
    }


    // ------------------------------------------------
    // WHEN CHECKBOX IS CHANGED, UPDATE COUNT IN BUTTON
    // ------------------------------------------------
    checkboxes.forEach(cb => {
        cb.addEventListener("change", updateSelectedSources);
    });

});





// Handle checkbox selection for edit mode
function loadExistingSources(sourceIds) {
    // Convert comma-separated IDs to array
    let ids = sourceIds.split(",");

    // Uncheck all first
    document.querySelectorAll(".Source-checkbox").forEach(cb => cb.checked = false);

    // Check matching IDs
    ids.forEach(id => {
        let checkbox = document.querySelector('.Source-checkbox[value="' + id + '"]');
        if (checkbox) {
            checkbox.checked = true;
        }
    });

    // Update UI (button text + hidden field)
    updateSelectedSources();
}

// Update dropdown button and hidden input
function updateSelectedSources() {
    let selectedIds = [];
    let selectedNames = [];

    document.querySelectorAll(".Source-checkbox").forEach(cb => {
        if (cb.checked) {
            selectedIds.push(cb.value);
            selectedNames.push(cb.nextElementSibling.innerText.trim());
        }
    });

    // Update hidden input
    document.getElementById("SourceName").value = selectedIds.join(",");

    // Update button text
    let btn = document.getElementById("SourceDropdown");
    btn.value = selectedNames.length > 0 ? selectedNames.join(", ") : "Select Source";
}

refNoLinks.forEach(link => {
    link.addEventListener("click", function (event) {
        event.preventDefault();
        SourceMaster.style.display = "block";

        document.getElementById("FeederName").value = this.getAttribute("data-FeederName");
        document.getElementById("SourceID").value = this.getAttribute("data-SourceID");
        document.getElementById("FeederId").value = this.getAttribute("data-id");

        // 🔥 New Code: load existing checked items
        let existingSourceIds = this.getAttribute("data-sourceid");
        if (existingSourceIds) {
            loadExistingSources(existingSourceIds);
        }

        if (deleteButton) {
            deleteButton.style.display = "inline-block";
        }
    });
});





Js:

<script>

    document.addEventListener("DOMContentLoaded", function () {
        var newButton = document.getElementById("newButton");
        var SourceMaster = document.getElementById("form");
        var refNoLinks = document.querySelectorAll(".refNoLink");
        var deleteButton = document.getElementById("deleteButton");
        var submitButton = document.getElementById("submitButton");
        var actionTypeInput = document.getElementById("actionType");

        if (newButton) {
            newButton.addEventListener("click", function () {
                SourceMaster.style.display = "block";
                document.getElementById("SourceID").value = "";     
                document.getElementById("FeederName").value = "";
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
    });

</script>

checkbox dropdown :

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
        <input type="hidden" id="SourceName" name="SourceID" />
    </div>

anchor tag which has sourceID values 

<a href="#" class="refNoLink" data-id="e43da0ea-9c88-4a1a-aafc-09bc234704cc" data-sourceid="f6851628-d446-4a7b-b569-31191c1d7d3a,085fa7a4-a5cc-424c-ae63-b524a2225f47" data-feedername="testing for feedeer" data-createdby="151514">
    testing for feedeer
</a>

i want to checked the value of source from for existing data when clicked on refNoLink
