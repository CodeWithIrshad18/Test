if (newButton) {
    newButton.addEventListener("click", function () {
        SourceMaster.style.display = "block";

        // Reset Source dropdown
        $("#SourceID").val("").trigger("change");

        // Reset Feeder dropdown (empty)
        $("#FeederID").html('<option value="">Select Feeder</option>');
        $("#FeederID").val("");

        // Reset DTR dropdown (empty)
        $("#DTR").html('<option value="">Select DTR</option>');
        $("#DTR").val("");

        // Clear other fields
        $("#FromDate").val("");
        $("#ToDate").val("");
        $("#DTRCapacity").val("");
        $("#NoOfConsumer").val("");
        $("#TypeOfInterruption").val("");
        hiddenInput.value = "";

        // Uncheck checkboxes
        checkboxes.forEach(cb => cb.checked = false);

        // Clear dropdown button
        dropdownBtn.value = "";

        // Hide delete button
        if (deleteButton) {
            deleteButton.style.display = "none";
        }
    });
}




if (newButton) {
    newButton.addEventListener("click", function () {
        SourceMaster.style.display = "block";

        document.getElementById("SourceID").value = "";
        document.getElementById("FeederID").value = "";
        document.getElementById("DTR").value = "";
        document.getElementById("FromDate").value = "";
        document.getElementById("ToDate").value = "";
        document.getElementById("DTRCapacity").value = "";
        document.getElementById("NoOfConsumer").value = "";
        document.getElementById("TypeOfInterruption").value = "";
        hiddenInput.value = "";


        checkboxes.forEach(cb => cb.checked = false);


        dropdownBtn.value = "";

        deleteButton.style.display = "none";
    });
}
