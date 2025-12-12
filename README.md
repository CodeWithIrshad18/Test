let viewInitialized = false;

document.getElementById("Location").addEventListener("click", function () {

    var modal = new bootstrap.Modal(document.getElementById("mapModal"));
    modal.show();

    setTimeout(function () {

        if (!viewInitialized) {
            viewInitialized = true;
            view.container = "viewDiv";   // Assign only ONCE
        }

        view.notifyChange("size");   // Refresh map layout
        loadExistingGeometry();

    }, 400);
});
sketch.cancel();



cdn :  <script src="https://js.arcgis.com/4.30/"></script>
error : My_Renewal.aspx:1172 Uncaught TypeError: view.resize is not a function
    at My_Renewal.aspx:1172:22
