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
