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

    // ------------------------------------------
    // NEW BUTTON CLICK → CLEAR FORM + DROPDOWN
    // ------------------------------------------
    if (newButton) {
        newButton.addEventListener("click", function () {
            SourceMaster.style.display = "block";

            document.getElementById("SourceID").value = "";
            document.getElementById("FeederName").value = "";
            hiddenInput.value = "";

            // Uncheck all checkboxes
            checkboxes.forEach(cb => cb.checked = false);

            // Reset dropdown label
            dropdownBtn.value = "Select Source";

            deleteButton.style.display = "none";
        });
    }

    // ------------------------------------------
    // REF-NO LINK CLICK → LOAD EXISTING DATA
    // ------------------------------------------
    refNoLinks.forEach(link => {
        link.addEventListener("click", function (event) {
            event.preventDefault();
            SourceMaster.style.display = "block";

            document.getElementById("FeederName").value = this.getAttribute("data-FeederName");
            document.getElementById("SourceID").value = this.getAttribute("data-SourceID");
            document.getElementById("FeederId").value = this.getAttribute("data-id");

            // Load checkbox selection
            let existingSourceIds = this.getAttribute("data-sourceid");
            if (existingSourceIds) {
                loadExistingSources(existingSourceIds);
            }

            if (deleteButton) {
                deleteButton.style.display = "inline-block";
            }
        });
    });

    // ------------------------------------------
    // SET ACTION TYPE ON SAVE
    // ------------------------------------------
    submitButton.addEventListener("click", function () {
        actionTypeInput.value = "save";
    });

    // ------------------------------------------
    // DELETE CONFIRMATION
    // ------------------------------------------
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

    // ------------------------------------------
    // UPDATE DROPDOWN WHEN CHECKBOX CHANGES
    // ------------------------------------------
    checkboxes.forEach(cb => {
        cb.addEventListener("change", updateSelectedSources);
    });

});


// --------------------------------------------------
// FUNCTION: LOAD EXISTING CHECKED SOURCES
// --------------------------------------------------
function loadExistingSources(sourceIds) {
    let ids = sourceIds.split(",");

    // Clear first
    document.querySelectorAll(".Source-checkbox").forEach(cb => cb.checked = false);

    // Check matching IDs
    ids.forEach(id => {
        let checkbox = document.querySelector('.Source-checkbox[value="' + id + '"]');
        if (checkbox) checkbox.checked = true;
    });

    updateSelectedSources();
}


// --------------------------------------------------
// FUNCTION: UPDATE BUTTON TEXT + HIDDEN FIELD
// --------------------------------------------------
function updateSelectedSources() {
    let selectedIds = [];

    document.querySelectorAll(".Source-checkbox").forEach(cb => {
        if (cb.checked) {
            selectedIds.push(cb.value);
        }
    });

    // Set hidden input (for submit)
    document.getElementById("SourceName").value = selectedIds.join(",");

    // Set dropdown button text → show count only
    let btn = document.getElementById("SourceDropdown");
    btn.value = selectedIds.length > 0 
        ? selectedIds.length + " selected"
        : "Select Source";
}

</script>





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
    });



    
function loadExistingSources(sourceIds) {
    let ids = sourceIds.split(",");


    document.querySelectorAll(".Source-checkbox").forEach(cb => cb.checked = false);

    ids.forEach(id => {
        let checkbox = document.querySelector('.Source-checkbox[value="' + id + '"]');
        if (checkbox) {
            checkbox.checked = true;
        }
    });

  
    updateSelectedSources();
}


function updateSelectedSources() {
    let selectedIds = [];
    let selectedNames = [];

    document.querySelectorAll(".Source-checkbox").forEach(cb => {
        if (cb.checked) {
            selectedIds.push(cb.value);
            selectedNames.push(cb.nextElementSibling.innerText.trim());
        }
    });


    document.getElementById("SourceName").value = selectedIds.join(",");

    let btn = document.getElementById("SourceDropdown");
    btn.value = selectedNames.length > 0 ? selectedNames.join(", ") : "Select Source";
}

</script>
