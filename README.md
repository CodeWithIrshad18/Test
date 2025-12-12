<script src="https://js.arcgis.com/4.30/"></script>

<script>
    var view, graphicsLayer, sketch;

    // -----------------------------------------------------
    // OPEN MODAL + REFRESH MAP
    // -----------------------------------------------------
    document.getElementById("Location").addEventListener("click", function () {

        var modal = new bootstrap.Modal(document.getElementById("mapModal"));
        modal.show();

        setTimeout(function () {

            // IMPORTANT: Never assign view.container again in 4.30
            view.notifyChange("size");     // refresh map layout
            loadExistingGeometry();        // load geometry if any

        }, 350);
    });


    // -----------------------------------------------------
    // ARC GIS OBJECTS LOADED HERE ONLY ONCE
    // -----------------------------------------------------
    require([
        "esri/Map",
        "esri/views/MapView",
        "esri/widgets/Sketch",
        "esri/layers/GraphicsLayer",
        "esri/Graphic",
        "esri/geometry/Polygon",
        "esri/geometry/Polyline",
        "esri/geometry/Point",
        "esri/widgets/Measurement"
    ], function (Map, MapView, Sketch, GraphicsLayer, Graphic, Polygon, Polyline, Point, Measurement) {

        graphicsLayer = new GraphicsLayer();

        const map = new Map({
            basemap: "satellite",
            layers: [graphicsLayer]
        });

        // The map view is created ONLY once
        view = new MapView({
            container: "viewDiv",   // stable, never reassigned
            map: map,
            center: [86.182457, 22.804294],
            zoom: 14
        });

        // ---------------- SKETCH TOOL ----------------
        sketch = new Sketch({
            view: view,
            layer: graphicsLayer,
            creationMode: "update"
        });

        view.ui.add(sketch, "top-right");

        sketch.on("create", function (event) {
            if (event.state === "complete") {
                saveCoordinates(event.graphic.geometry);
            }
        });

        sketch.on("update", function (event) {
            if (event.state === "complete") {
                saveCoordinates(event.graphics[0].geometry);
            }
        });

        // ---------------- Measurement Tool ----------------
        const measurement = new Measurement({ view: view });
        view.ui.add(measurement, "bottom-right");

        // Basemap change
        document.getElementById("basemapSelect").addEventListener("change", function () {
            map.basemap = this.value;
        });


        // -----------------------------------------------------
        // SAVE COORDINATES
        // -----------------------------------------------------
        function saveCoordinates(geometry) {
            let coords = "";

            if (geometry.type === "point") {
                coords = geometry.latitude + "," + geometry.longitude;
            }
            else if (geometry.type === "polyline") {
                coords = geometry.paths[0].map(p => p[1] + "," + p[0]).join("; ");
            }
            else if (geometry.type === "polygon") {
                coords = geometry.rings[0].map(p => p[1] + "," + p[0]).join("; ");
            }

            document.getElementById("Location").value = coords;
            console.log("Saved:", coords);
        }


        // -----------------------------------------------------
        // LOAD EXISTING GEOMETRY SAFELY
        // -----------------------------------------------------
        window.loadExistingGeometry = function () {

            graphicsLayer.removeAll();
            sketch.cancel();  // cancel old edit session

            let value = document.getElementById("Location").value.trim();
            if (value === "") return;

            let coordPairs = value.split(";").map(v => v.trim());

            let points = coordPairs.map(c => {
                let p = c.split(",");
                return [parseFloat(p[1]), parseFloat(p[0])]; // lng, lat
            });

            let graphic;

            if (points.length === 1) {
                // POINT
                graphic = new Graphic({
                    geometry: new Point({
                        longitude: points[0][0],
                        latitude: points[0][1]
                    }),
                    symbol: { type: "simple-marker", color: "red" }
                });
            }
            else if (
                points.length > 1 &&
                points[0][0] === points[points.length - 1][0] &&
                points[0][1] === points[points.length - 1][1]
            ) {
                // POLYGON
                graphic = new Graphic({
                    geometry: new Polygon({
                        rings: [points],
                        spatialReference: { wkid: 4326 }
                    }),
                    symbol: {
                        type: "simple-fill",
                        color: [0, 0, 255, 0.3],
                        outline: { color: "blue", width: 2 }
                    }
                });
            }
            else {
                // POLYLINE
                graphic = new Graphic({
                    geometry: new Polyline({
                        paths: [points],
                        spatialReference: { wkid: 4326 }
                    }),
                    symbol: {
                        type: "simple-line",
                        color: "red",
                        width: 2
                    }
                });
            }

            graphicsLayer.add(graphic);
            view.goTo(graphic);

            sketch.update([graphic]);  // enable editing
        };
    });
</script>




Js :    
   <script src="https://js.arcgis.com/4.30/"></script>
    <script>
        let viewInitialized = false;
        var view, graphicsLayer, sketch;

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



        require([
            "esri/Map",
            "esri/views/MapView",
            "esri/widgets/Sketch",
            "esri/layers/GraphicsLayer",
            "esri/Graphic",
            "esri/geometry/Polygon",
            "esri/geometry/Polyline",
            "esri/geometry/Point",
            "esri/widgets/Measurement"
        ], function (Map, MapView, SketchClass, GraphicsLayerClass,
            Graphic, Polygon, Polyline, Point, Measurement) {

            graphicsLayer = new GraphicsLayerClass();

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

            // ----------- SKETCH TOOL -----------
            sketch = new SketchClass({
                view: view,
                layer: graphicsLayer,
                creationMode: "update"
            });

            view.ui.add(sketch, "top-right");

            sketch.on("update", function (event) {
                if (event.state === "complete") {
                    saveCoordinates(event.graphics[0].geometry);
                }
            });

            sketch.on("create", function (event) {
                if (event.state === "complete") {
                    saveCoordinates(event.graphic.geometry);
                }
            });

            // Measurement tool
            const measurement = new Measurement({ view: view });
            view.ui.add(measurement, "bottom-right");

            document.getElementById("basemapSelect").addEventListener("change", function () {
                map.basemap = this.value;
            });

            // ---------- SAVE COORDINATES ----------
            function saveCoordinates(geometry) {
                let coords = "";

                if (geometry.type === "point") {
                    coords = geometry.latitude + "," + geometry.longitude;
                }

                if (geometry.type === "polyline") {
                    coords = geometry.paths[0].map(p => p[1] + "," + p[0]).join("; ");
                }

                if (geometry.type === "polygon") {
                    coords = geometry.rings[0].map(p => p[1] + "," + p[0]).join("; ");
                }

                console.log("Saved Coordinates:", coords);

                document.getElementById("Location").value = coords;
        }

        // ---------- LOAD EXISTING SHAPE ----------
        window.loadExistingGeometry = function () {
            graphicsLayer.removeAll();
            sketch.cancel(); 
            let value = document.getElementById("Location").value.trim();
            if (value === "") return;

            let coordPairs = value.split(";").map(v => v.trim());
            let points = coordPairs.map(c => {
                let parts = c.split(",");
                return [parseFloat(parts[1]), parseFloat(parts[0])]; // longitude, latitude
            });

            let graphic;

            if (points.length === 1) {
                // POINT
                graphic = new Graphic({
                    geometry: new Point({
                        latitude: points[0][1],
                        longitude: points[0][0]
                    }),
                    symbol: { type: "simple-marker", color: "red" }
                });
            }
            else if (points.length > 1 && points[0][0] === points[points.length - 1][0] &&
                points[0][1] === points[points.length - 1][1]) {
                // POLYGON
                graphic = new Graphic({
                    geometry: new Polygon({
                        rings: [points],
                        spatialReference: { wkid: 4326 }
                    }),
                    symbol: {
                        type: "simple-fill",
                        color: [0, 0, 255, 0.3],
                        outline: { color: "blue", width: 2 }
                    }
                });
            }
            else {
                // POLYLINE
                graphic = new Graphic({
                    geometry: new Polyline({
                        paths: [points],
                        spatialReference: { wkid: 4326 }
                    }),
                    symbol: {
                        type: "simple-line",
                        color: "red",
                        width: 2
                    }
                });
            }

            graphicsLayer.add(graphic);

            view.goTo(graphic);
            sketch.cancel();
            sketch.update([graphic]);  // allow editing immediately
        };
    });
    </script>

Location : 

                              <div class="form-group col-md-3 mb-1">
    <label for="Location" class="m-0 mr-2 p-0 col-form-label-sm font-weight-bold fs-6">
        Location:<span class="text-danger">*</span>
    </label>
    <asp:TextBox ID="Location" ClientIDMode="Static" runat="server" CssClass="form-control form-control-sm col-12 input" />
</div>

modal : 

    <div class="modal fade" id="mapModal" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog modal-xl">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title">Select Location</h5>
               <button type="button" class="close" data-dismiss="modal">&times;</button>
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

now map is showing in first attempt after 2nd click map is not working it is destroyed 
