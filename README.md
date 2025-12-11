<div class="form-group col-md-3 mb-1">
    <label for="Location" class="m-0 mr-2 p-0 col-form-label-sm font-weight-bold fs-6">
        Location:<span class="text-danger">*</span>
    </label>
    <asp:TextBox ID="Location" runat="server" CssClass="form-control form-control-sm col-12 input" />
</div>

<!-- MAP MODAL -->
<div class="modal fade" id="mapModal" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog modal-xl">
        <div class="modal-content">

            <div class="modal-header">
                <h5 class="modal-title">Select Location</h5>
                <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
            </div>

            <div class="modal-body">
                <div id="viewDiv" style="height:600px; width:100%;"></div>

                <div class="mt-2">
                    <label>Change Basemap:</label>
                    <select id="basemapSelect" class="form-control" style="width:250px;">
                        <option value="satellite">Satellite</option>
                        <option value="topo-vector">Topographic</option>
                        <option value="streets-vector">Streets</option>
                        <option value="hybrid">Hybrid</option>
                        <option value="dark-gray-vector">Dark Gray</option>
                    </select>
                </div>
            </div>

        </div>
    </div>
</div>

<script src="https://js.arcgis.com/4.30/"></script>

<script>
    // OPEN MODAL ON TEXTBOX CLICK
    document.getElementById("Location").addEventListener("click", function () {
        var modal = new bootstrap.Modal(document.getElementById("mapModal"));
        modal.show();

        setTimeout(function () {
            if (view) {
                view.container = "viewDiv";   // Fix resize inside modal
            }
        }, 400);
    });

    var view;

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

        const measurement = new Measurement({
            view: view
        });
        view.ui.add(measurement, "bottom-right");

        document.getElementById("basemapSelect").addEventListener("change", function () {
            map.basemap = this.value;
        });


        // Convert geometry to lat/long format
        function getLatLongCoords(geometry) {
            let geom = webMercatorUtils.webMercatorToGeographic(geometry);

            if (geom.type === "point") {
                return geom.latitude + "," + geom.longitude;
            }

            if (geom.type === "polyline") {
                return geom.paths[0]
                    .map(p => p[1] + "," + p[0])  // lat,long
                    .join("; ");
            }

            if (geom.type === "polygon") {
                return geom.rings[0]
                    .map(p => p[1] + "," + p[0])  // lat,long
                    .join("; ");
            }
        }

        // When drawing is completed
        sketch.on("create", function (event) {
            if (event.state === "complete") {
                let coords = getLatLongCoords(event.graphic.geometry);

                document.getElementById("Location").value = coords;

                console.log("Created Geometry:", coords);
            }
        });

        // When user edits polygon/line/point
        sketch.on("update", function (event) {
            if (event.graphics.length > 0) {

                let geometry = event.graphics[0].geometry;

                let coords = getLatLongCoords(geometry);

                document.getElementById("Location").value = coords;

                console.log("Updated Geometry:", coords);
            }
        });

    });
</script>




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
