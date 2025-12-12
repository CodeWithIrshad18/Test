<script src="https://js.arcgis.com/4.30/"></script>

<script>
/*
  Robust pattern for ArcGIS Map inside a Bootstrap modal:
  - Create MapView once with container: null (detached)
  - On modal 'shown' attach view.container = "viewDiv", wait view.when(), then load existing geometry
  - On modal 'hidden' detach view.container = null
  - Add widgets only once
*/

var view, graphicsLayer, sketch, measurement;
var widgetsAdded = false; // guard to add widgets only once

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
    Graphic, Polygon, Polyline, Point, MeasurementClass) {

    // create map and graphics layer
    graphicsLayer = new GraphicsLayerClass();

    var map = new Map({
        basemap: "satellite",
        layers: [graphicsLayer]
    });

    // create the view but keep it detached from DOM initially
    view = new MapView({
        container: null, // IMPORTANT: don't attach yet
        map: map,
        center: [86.182457, 22.804294],
        zoom: 14
    });

    // create sketch and measurement instances (they reference view)
    // but we will add them to view.ui only when view is attached (shown)
    sketch = new SketchClass({
        view: view,
        layer: graphicsLayer,
        creationMode: "update"
    });

    measurement = new MeasurementClass({
        view: view
    });

    // respond to create/update events to save coordinates
    sketch.on("create", function (event) {
        if (event.state === "complete") {
            saveCoordinates(event.graphic.geometry);
        }
    });

    sketch.on("update", function (event) {
        // event.graphics may vary depending on update action; handle first graphic
        if (event.state === "complete" && event.graphics && event.graphics.length) {
            saveCoordinates(event.graphics[0].geometry);
        }
    });

    // basemap select handler (will work even while detached)
    document.getElementById("basemapSelect").addEventListener("change", function () {
        map.basemap = this.value;
    });

    // Save geometry into textbox (expects geometry in geographic (lat,lng) or 4326)
    function saveCoordinates(geometry) {
        var coords = "";

        // If geometry may be in webMercator you should convert; here we assume incoming geometry
        // is geographic (4326) because we construct it that way when loading.
        if (!geometry) return;

        if (geometry.type === "point") {
            coords = geometry.latitude + "," + geometry.longitude;
        }
        else if (geometry.type === "polyline") {
            coords = geometry.paths[0].map(p => p[1] + "," + p[0]).join("; ");
        }
        else if (geometry.type === "polygon") {
            coords = geometry.rings[0].map(p => p[1] + "," + p[0]).join("; ");
        }

        console.log("Saved Coordinates:", coords);
        document.getElementById("Location").value = coords;
    }

    // helper to construct a Graphic from textbox string "lat,lng; lat,lng; ..."
    function buildGraphicFromText(txt) {
        txt = (txt || "").trim();
        if (!txt) return null;

        var pairs = txt.split(";").map(s => s.trim()).filter(s => s.length);
        var pts = pairs.map(function (pair) {
            var parts = pair.split(",").map(t => t.trim());
            // NOTE: input format is lat,long -> parts[0]=lat, parts[1]=long
            var lat = parseFloat(parts[0]);
            var lon = parseFloat(parts[1]);
            return [lon, lat]; // return [long, lat] suitable for rings/paths
        });

        if (pts.length === 0) return null;

        if (pts.length === 1) {
            // single point
            return new Graphic({
                geometry: new Point({
                    latitude: pts[0][1],
                    longitude: pts[0][0],
                    spatialReference: { wkid: 4326 }
                }),
                symbol: { type: "simple-marker", color: "red" }
            });
        }

        // check if polygon: first === last
        var first = pts[0], last = pts[pts.length - 1];
        var isClosed = (first[0] === last[0] && first[1] === last[1]);

        if (isClosed) {
            return new Graphic({
                geometry: new Polygon({
                    rings: [pts],
                    spatialReference: { wkid: 4326 }
                }),
                symbol: {
                    type: "simple-fill",
                    color: [0, 0, 255, 0.3],
                    outline: { color: "blue", width: 2 }
                }
            });
        } else {
            return new Graphic({
                geometry: new Polyline({
                    paths: [pts],
                    spatialReference: { wkid: 4326 }
                }),
                symbol: {
                    type: "simple-line",
                    color: "red",
                    width: 2
                }
            });
        }
    }

    // function to load existing geometry from textbox when view is attached
    window.loadExistingGeometry = function () {
        graphicsLayer.removeAll();

        var value = document.getElementById("Location").value;
        if (!value || !value.trim()) return;

        var graphic = buildGraphicFromText(value);
        if (!graphic) return;

        graphicsLayer.add(graphic);

        // ensure view is ready, then zoom to graphic and enable editing on it
        view.when().then(function () {
            view.goTo(graphic).catch(function (err) { console.warn("goTo error:", err); });
            // enable editing via sketch
            sketch.update([graphic]);
        }).catch(function (err) {
            console.warn("view.when error", err);
        });
    };


    // ---------- Bootstrap modal hooks ----------
    var modalEl = document.getElementById("mapModal");

    // When modal shows: attach view to DOM and load geometry
    modalEl.addEventListener('shown.bs.modal', function () {
        // attach view to DOM if not already attached
        if (view && view.container !== "viewDiv") {
            view.container = "viewDiv";
        }

        // add widgets only once (guard with widgetsAdded)
        if (!widgetsAdded) {
            view.ui.add(sketch, "top-right");
            view.ui.add(measurement, "bottom-right");
            widgetsAdded = true;
        }

        // Wait until view is ready then load geometry (small timeout helps with CSS animation)
        view.when().then(function () {
            setTimeout(function () {
                loadExistingGeometry();
            }, 200);
        }).catch(function (err) {
            console.warn("view.when error after shown modal:", err);
        });
    });

    // When modal hides: detach view to avoid layout issues on next open
    modalEl.addEventListener('hidden.bs.modal', function () {
        try {
            if (view && view.container) {
                // detach from DOM but keep view object alive
                view.container = null;
            }
        } catch (e) {
            console.warn("error detaching view:", e);
        }
    });

    // Optionally open modal by clicking the textbox (you can keep this or remove if you call modal elsewhere)
    document.getElementById("Location").addEventListener("click", function () {
        var m = new bootstrap.Modal(modalEl);
        m.show();
    });

}); // end require
</script>



this is my js    
   <script src="https://js.arcgis.com/4.30/"></script>
    <script>
        var view, graphicsLayer, sketch;

        document.getElementById("Location").addEventListener("click", function () {

            var modal = new bootstrap.Modal(document.getElementById("mapModal"));
            modal.show();

            setTimeout(function () {
                view.container = "viewDiv";

                loadExistingGeometry();      // <<< LOAD EXISTING SHAPE
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
            sketch.update([graphic]);  // allow editing immediately
        };
    });
    </script>

and this is my modal and textbox for existing data 

                              <div class="form-group col-md-3 mb-1">
    <label for="Location" class="m-0 mr-2 p-0 col-form-label-sm font-weight-bold fs-6">
        Location:<span class="text-danger">*</span>
    </label>
    <asp:TextBox ID="Location" ClientIDMode="Static" runat="server" CssClass="form-control form-control-sm col-12 input" />
</div>

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

issue : when user clicks on Location first attempt it shows correctly then user close the modal then again click on Location it shows same no problem then 3rd attempt when user clicks map is not showing it shows something like a line . i have a reference image for that . please see the issue and correct them 
