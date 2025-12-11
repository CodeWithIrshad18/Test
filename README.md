require([
    "esri/Map",
    "esri/views/MapView",
    "esri/widgets/Sketch",
    "esri/layers/GraphicsLayer",
    "esri/widgets/Measurement",
    "esri/geometry/support/webMercatorUtils"
], function (Map, MapView, Sketch, GraphicsLayer, Measurement, webMercatorUtils) {

    const graphicsLayer = new GraphicsLayer();

    const map = new Map({
        basemap: "satellite",
        layers: [graphicsLayer]
    });

    view = new MapView({
        container: "viewDiv",
        map: map,
        center: [86.182457, 22.804294],
        zoom: 14
    });

    const sketch = new Sketch({
        view: view,
        layer: graphicsLayer,
        creationMode: "update"
    });
    view.ui.add(sketch, "top-right");

    // Capture coordinates from drawing
    sketch.on("create", function (event) {

        if (event.state === "complete") {
            let coords = "";

            // Convert geometry to lat-long
            let geom = webMercatorUtils.webMercatorToGeographic(event.graphic.geometry);

            // POINT
            if (geom.type === "point") {
                coords = geom.latitude + "," + geom.longitude;
            }

            // POLYLINE
            if (geom.type === "polyline") {
                coords = geom.paths[0]
                    .map(p => p[1] + "," + p[0])  // lat,long
                    .join("; ");
            }

            // POLYGON
            if (geom.type === "polygon") {
                coords = geom.rings[0]
                    .map(p => p[1] + "," + p[0])  // lat,long
                    .join("; ");
            }

            // Write to textbox
            document.getElementById("Location").value = coords;
        }
    });

    const measurement = new Measurement({
        view: view
    });
    view.ui.add(measurement, "bottom-right");

    document.getElementById("basemapSelect").addEventListener("change", function () {
        map.basemap = this.value;
    });
});




protected void btnSave_Click(object sender, EventArgs e)
{
    ApprovingAuth_Record.UnbindData();

    // 1️⃣ --- Create DataTable for multiple rows ---
    DataTable dt = new DataTable();
    dt.Columns.Add("Approving_Authority", typeof(string));
    dt.Columns.Add("CreatedBy", typeof(string));
    dt.Columns.Add("CreatedOn", typeof(DateTime));

    CheckBoxList chk = (CheckBoxList)ApprovingAuth_Record.Rows[0]
                        .FindControl("Approving_Authority");

    // 2️⃣ --- Add selected items as separate rows ---
    foreach (ListItem item in chk.Items)
    {
        if (item.Selected)
        {
            DataRow dr = dt.NewRow();
            dr["Approving_Authority"] = item.Value;
            dr["CreatedBy"] = Session["EmpCode"].ToString();
            dr["CreatedOn"] = DateTime.Now;

            dt.Rows.Add(dr);
        }
    }

    // 3️⃣ --- Check if nothing selected ---
    if (dt.Rows.Count == 0)
    {
        ScriptManager.RegisterStartupScript(
            this, GetType(), "alert", "alert('Please select Approving Authority');", true);
        return;
    }

    // 4️⃣ --- Merge with your PageRecordDataSet ---
    // IMPORTANT: replace "ApprovingAuthority" with your actual table name
    PageRecordDataSet.Tables["ApprovingAuthority"].Clear();
    PageRecordDataSet.Tables["ApprovingAuthority"].Merge(dt);

    // 5️⃣ --- Now normal SAVE() will insert all rows ---
    bool result = Save();

    if (result)
    {
        GetRecords(GetFilterCondition(), ApprovingAuth_Records.PageSize, 0, "");
        ApprovingAuth_Records.BindData();
        PageRecordDataSet.Clear();
        ApprovingAuth_Record.BindData();

        btnSave.Visible = false;
        btnDelete.Visible = false;

        MyMsgBox.show(FNAAPP.Control.MyMsgBox.MessageType.Success,
                      "Record saved successfully !");
    }
    else
    {
        MyMsgBox.show(FNAAPP.Control.MyMsgBox.MessageType.Errors,
                      "Error while saving !");
    }
}




coordinates of textbox i am receiving : 
2608651.4582576603,9593517.310297024; 2608555.911972311,9593664.212710747; 2608487.8352440004,9593458.78819725; 2608651.4582576603,9593517.310297024

my js :
     <script>
         document.getElementById("Location").addEventListener("click", function () {
             var modal = new bootstrap.Modal(document.getElementById("mapModal"));
             modal.show();

             setTimeout(function () {
                 view.container = "viewDiv";   // refresh map in modal
             }, 400);
         });

         var view;

         require([
             "esri/Map",
             "esri/views/MapView",
             "esri/widgets/Sketch",
             "esri/layers/GraphicsLayer",
             "esri/widgets/Measurement"
         ], function (Map, MapView, Sketch, GraphicsLayer, Measurement) {

             const graphicsLayer = new GraphicsLayer();

             const map = new Map({
                 basemap: "satellite",
                 layers: [graphicsLayer]
             });

             view = new MapView({
                 container: "viewDiv",
                 map: map,
                 center: [86.182457, 22.804294],
                 zoom: 14
             });

             const sketch = new Sketch({
                 view: view,
                 layer: graphicsLayer,
                 creationMode: "update"
             });
             view.ui.add(sketch, "top-right");

             // Capture coordinates from drawing
             sketch.on("create", function (event) {

                 if (event.state === "complete") {
                     let coords = "";

                     if (event.graphic.geometry.type === "point") {
                         coords = event.graphic.geometry.latitude + "," +
                             event.graphic.geometry.longitude;
                     }

                     if (event.graphic.geometry.type === "polyline") {
                         coords = event.graphic.geometry.paths[0]
                             .map(p => p[1] + "," + p[0])
                             .join("; ");
                     }

                     if (event.graphic.geometry.type === "polygon") {
                         coords = event.graphic.geometry.rings[0]
                             .map(p => p[1] + "," + p[0])
                             .join("; ");
                     }

                     // Fill into your textbox
                     document.getElementById("Location").value = coords;
    }
});

             const measurement = new Measurement({
                 view: view
             });
             view.ui.add(measurement, "bottom-right");

             document.getElementById("basemapSelect").addEventListener("change", function () {
                 map.basemap = this.value;
             });
         });
     </script>
