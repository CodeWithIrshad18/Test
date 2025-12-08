<script>
document.addEventListener("DOMContentLoaded", function () {

    /* -------------------------------------------------------
       ELEMENT REFERENCES
    --------------------------------------------------------*/
    const sourceCheckboxes = document.querySelectorAll(".Source-checkbox");
    const sourceHidden = document.getElementById("SourceID");
    const sourceDropdown = document.getElementById("SourceDropdown");

    const feederHidden = document.getElementById("FeederID");
    const feederDropdown = document.getElementById("FeederDropdown");
    const feederList = document.getElementById("FeederList");

    /* -------------------------------------------------------
       SOURCE DROPDOWN LOGIC
    --------------------------------------------------------*/
    function updateSources() {
        let ids = [];
        let names = [];

        sourceCheckboxes.forEach(cb => {
            if (cb.checked) {
                ids.push(cb.value);
                names.push(cb.nextElementSibling.innerText.trim());
            }
        });

        sourceHidden.value = ids.join(",");
        sourceDropdown.value = names.length ? names.join(", ") : "Select Source";

        loadFeeders(); // Reload feeders when source changes
    }

    sourceCheckboxes.forEach(cb => cb.addEventListener("change", updateSources));


    /* -------------------------------------------------------
       LOAD FEEDERS BY SOURCE
    --------------------------------------------------------*/
    function loadFeeders() {
        let sourceIdsString = sourceHidden.value;

        if (!sourceIdsString) {
            feederList.innerHTML = "<li class='ms-2'>Select source first</li>";
            feederDropdown.value = "Select Feeder";
            feederHidden.value = "";
            return;
        }

        let sourceIds = sourceIdsString.split(",");

        fetch('/Master/GetFeedersBySource', {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify(sourceIds)
        })
        .then(res => res.json())
        .then(data => renderFeederList(data));
    }


    /* -------------------------------------------------------
       RENDER FEEDER LIST
    --------------------------------------------------------*/
    function renderFeederList(data) {
        feederList.innerHTML = "";

        if (data.length === 0) {
            feederList.innerHTML = "<li class='ms-2 text-danger'>No Feeder Found</li>";
            return;
        }

        data.forEach(item => {
            let id = item.ID || item.id;
            let name = item.FeederName || item.feederName;

            feederList.innerHTML += `
                <li style="margin-left:5%;">
                    <div class="form-check">
                        <input type="checkbox" class="form-check-input Feeder-checkbox"
                               value="${id}" id="f_${id}">
                        <label class="form-check-label" for="f_${id}">${name}</label>
                    </div>
                </li>`;
        });

        attachFeederEvents();
        loadExistingFeeders(); // ⭐ Mark existing feeders when editing
    }


    /* -------------------------------------------------------
       FEEDER CHECKBOX EVENTS
    --------------------------------------------------------*/
    function attachFeederEvents() {
        const feederCheckboxes = document.querySelectorAll(".Feeder-checkbox");

        feederCheckboxes.forEach(cb => {
            cb.addEventListener("change", updateFeederSelection);
        });
    }

    function updateFeederSelection() {
        const feederCheckboxes = document.querySelectorAll(".Feeder-checkbox");

        let ids = [];
        let names = [];

        feederCheckboxes.forEach(cb => {
            if (cb.checked) {
                ids.push(cb.value);
                names.push(cb.nextElementSibling.innerText.trim());
            }
        });

        feederHidden.value = ids.join(",");
        feederDropdown.value = names.length ? names.join(", ") : "Select Feeder";
    }


    /* -------------------------------------------------------
       LOAD EXISTING SOURCE CHECKBOXES IN EDIT MODE
    --------------------------------------------------------*/
    function loadExistingSources(sourceIds) {
        let ids = sourceIds.split(",");

        sourceCheckboxes.forEach(cb => cb.checked = false);

        ids.forEach(id => {
            let box = document.querySelector('.Source-checkbox[value="' + id + '"]');
            if (box) box.checked = true;
        });

        updateSources(); // reload feeders automatically
    }


    /* -------------------------------------------------------
       LOAD EXISTING FEEDERS IN EDIT MODE (AFTER LIST RENDER)
    --------------------------------------------------------*/
    function loadExistingFeeders() {
        let feederIds = feederHidden.value;
        if (!feederIds) return;

        let ids = feederIds.split(",");

        setTimeout(() => {
            ids.forEach(id => {
                let box = document.querySelector('.Feeder-checkbox[value="' + id + '"]');
                if (box) box.checked = true;
            });

            updateFeederSelection();
        }, 200);
    }


    /* -------------------------------------------------------
       EDIT MODE CLICK (refNoLink)
    --------------------------------------------------------*/
    document.querySelectorAll(".refNoLink").forEach(link => {

        link.addEventListener("click", function (e) {
            e.preventDefault();

            document.getElementById("form").style.display = "block";

            // Fill other fields
            document.getElementById("DTRCapacity").value = this.dataset.dtrcapacity;
            document.getElementById("DTRName").value = this.dataset.dtrname;
            document.getElementById("NoOfConsumer").value = this.dataset.noofconsumer;
            document.getElementById("DTRId").value = this.dataset.id;

            // Load existing SOURCES
            let existingSources = this.dataset.sourceid || "";
            if (existingSources) loadExistingSources(existingSources);

            // Set existing FEEDERS
            feederHidden.value = this.dataset.feederid || "";
        });
    });


    /* -------------------------------------------------------
       NEW BUTTON RESET FORM
    --------------------------------------------------------*/
    let newButton = document.getElementById("newButton");
    if (newButton) {
        newButton.addEventListener("click", function () {

            document.getElementById("form").style.display = "block";

            sourceHidden.value = "";
            feederHidden.value = "";

            sourceCheckboxes.forEach(cb => cb.checked = false);

            sourceDropdown.value = "Select Source";
            feederDropdown.value = "Select Feeder";

            feederList.innerHTML = "<li class='ms-2'>Select source first</li>";
        });
    }

});
</script>





<script>
    document.addEventListener("DOMContentLoaded", function () {

        const sourceCheckboxes = document.querySelectorAll(".Source-checkbox");
        const sourceHidden = document.getElementById("SourceID");
        const sourceDropdownBtn = document.getElementById("SourceDropdown");

        function updateSources() {
            let ids = [];
            let names = [];

            sourceCheckboxes.forEach(cb => {
                if (cb.checked) {
                    ids.push(cb.value);
                    names.push(cb.nextElementSibling.innerText.trim());
                }
            });

            sourceHidden.value = ids.join(",");
            sourceDropdownBtn.value = names.length ? names.join(", ") : "Select Source";

            loadFeeders();
        }

        sourceCheckboxes.forEach(cb => cb.addEventListener("change", updateSources));

        // ---------------- FEEDER DROPDOWN ----------------
        function loadFeeders() {

            let sourceIdsString = document.getElementById("SourceID").value;

            // console.log("Source IDs:"+sourceIdsString);

            if (!sourceIdsString) {
                document.getElementById("FeederList").innerHTML =
                    "<li class='ms-2'>Select source first</li>";

                document.getElementById("FeederDropdown").value = "Select Feeder";
                document.getElementById("FeederID").value = "";

                return;
            }

         
            let sourceIds = sourceIdsString.split(",");

            fetch('/Master/GetFeedersBySource', {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify(sourceIds)  // <-- Always works
            })
            .then(response => response.json())
            .then(data => {
                renderFeederList(data);
            });
        }

    function renderFeederList(data) {
        let feederList = document.getElementById("FeederList");
        feederList.innerHTML = "";

        if (data.length === 0) {
            feederList.innerHTML = "<li class='ms-2 text-danger'>No Feeder Found</li>";
            return;
        }

        data.forEach(item => {

            let id = item.ID || item.id;
            let name = item.FeederName || item.feederName;

            console.log("Parsed:", id, name);

            feederList.innerHTML += `
                <li style="margin-left:5%;">
                    <div class="form-check">
                        <input type="checkbox" class="form-check-input Feeder-checkbox"
                               value="${id}" id="f_${id}" />
                        <label class="form-check-label" for="f_${id}">${name}</label>
                    </div>
                </li>`;
        });

        attachFeederEvents();
    }

        function attachFeederEvents() {
            const feederCheckboxes = document.querySelectorAll(".Feeder-checkbox");
            const feederHidden = document.getElementById("FeederID");
            const feederDropdownBtn = document.getElementById("FeederDropdown");

            function updateFeeders() {
                let ids = [];
                let names = [];

                feederCheckboxes.forEach(cb => {
                    if (cb.checked) {
                        ids.push(cb.value);
                        names.push(cb.nextElementSibling.innerText.trim());
                    }
                });

                feederHidden.value = ids.join(",");
                feederDropdownBtn.value = names.length ? names.join(", ") : "Select Feeder";
            }

            feederCheckboxes.forEach(cb => cb.addEventListener("change", updateFeeders));
        }

    });
</script>
<script>

    document.addEventListener("DOMContentLoaded", function () {

        var newButton = document.getElementById("newButton");
        var SourceMaster = document.getElementById("form");
        var refNoLinks = document.querySelectorAll(".refNoLink");
        var deleteButton = document.getElementById("deleteButton");
        var submitButton = document.getElementById("submitButton");
        var actionTypeInput = document.getElementById("actionType");

        const checkboxes = document.querySelectorAll(".Source-checkbox");
        const hiddenInput = document.getElementById("SourceID");
        const dropdownBtn = document.getElementById("SourceDropdown");


        if (newButton) {
            newButton.addEventListener("click", function () {
                SourceMaster.style.display = "block";

                document.getElementById("SourceID").value = "";
                document.getElementById("DTRName").value = "";
                document.getElementById("DTRCapacity").value = "";
                document.getElementById("NoOfConsumer").value = "";
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

                document.getElementById("DTRCapacity").value = this.getAttribute("data-DTRCapacity");
                document.getElementById("DTRName").value = this.getAttribute("data-DTRName");
                document.getElementById("NoOfConsumer").value = this.getAttribute("data-NoOfConsumer");
                document.getElementById("DTRId").value = this.getAttribute("data-id");

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

        document.getElementById("SourceID").value = selectedIds.join(",");


        let btn = document.getElementById("SourceDropdown");
        btn.value = selectedIds.length > 0
            ? selectedIds.length + " selected"
            : "Select Source";
    }

</script>

hidden field with drodpdown 

<div class="col-md-3">
                        <div class="dropdown">
                            <input class="dropdown-toggle form-control form-control-sm custom-select text-start" type="button" id="FeederDropdown" data-bs-toggle="dropdown" aria-expanded="false" value="Select Feeder">

                            <ul class="dropdown-menu w-100" aria-labelledby="FeederDropdown" id="FeederList" style="">
                         
                            </ul>
                        </div>

                        <input type="hidden" id="FeederID" name="FeederID" value="e43da0ea-9c88-4a1a-aafc-09bc234704cc,6f0e23ce-e251-46e2-a0fb-62bf13c1caa7">
                    </div>
