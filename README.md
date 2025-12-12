<script>
    var view, graphicsLayer, sketch;

    require([
        "esri/Map",
        "esri/views/MapView",
        "esri/widgets/Sketch",
        "esri/layers/GraphicsLayer",
        "esri/Graphic",
        "esri/geometry/Polygon",
        "esri/geometry/Polyline",
        "esri/geometry/Point",
        "esri/widgets/Measurement",
        "esri/geometry/support/webMercatorUtils"
    ], function (
        Map, MapView, SketchClass, GraphicsLayerClass,
        Graphic, Polygon, Polyline, Point, Measurement, webMercatorUtils
    ) {

        // create layer and map (once)
        graphicsLayer = new GraphicsLayerClass();

        const map = new Map({
            basemap: "satellite",
            layers: [graphicsLayer]
        });

        // create view but DO NOT attach to DOM yet (container: null)
        view = new MapView({
            container: null,        // important: start detached
            map: map,
            center: [86.182457, 22.804294],
            zoom: 14
        });

        // Create sketch but don't attach UI until the view is attached
        sketch = new SketchClass({
            view: view,
            layer: graphicsLayer,
            creationMode: "update"
        });

        // measurement widget
        const measurement = new Measurement({ view: view });

        // listen to sketch events to save geometry
        sketch.on("create", function (event) {
            if (event.state === "complete") {
                saveCoordinates(event.graphic.geometry);
            }
        });

        sketch.on("update", function (event) {
            if (event.state === "complete" && event.graphics && event.graphics.length) {
                saveCoordinates(event.graphics[0].geometry);
            }
        });

        // basemap select
        document.getElementById("basemapSelect").addEventListener("change", function () {
            map.basemap = this.value;
        });

        // Save coordinates (polygon/line/point) into textbox and console
        function saveCoordinates(geometry) {
            // Ensure geographic lat/long (geometry may already be in WGS84)
            let geom = geometry;
            // if geometry is in webMercator, convert (optional)
            try {
                if (geometry.spatialReference && geometry.spatialReference.isWebMercator) {
                    geom = webMercatorUtils.webMercatorToGeographic(geometry);
                }
            } catch (e) {
                // ignore if conversion fails or not needed
            }

            let coords = "";
            if (geom.type === "point") {
                coords = geom.latitude + "," + geom.longitude;
            } else if (geom.type === "polyline") {
                coords = geom.paths[0].map(p => `${p[1]},${p[0]}`).join("; ");
            } else if (geom.type === "polygon") {
                coords = geom.rings[0].map(p => `${p[1]},${p[0]}`).join("; ");
            }

            console.log("Saved Coordinates:", coords);
            document.getElementById("Location").value = coords;
        }

        // Build a graphic from lat,lng; lat,lng string (WGS84 assumed)
        function buildGraphicFromText(txt) {
            txt = (txt || "").trim();
            if (!txt) return null;

            const pairs = txt.split(";").map(s => s.trim()).filter(s => s.length);
            const pts = pairs.map(function (pair) {
                const parts = pair.split(",").map(p => p.trim());
                const lat = parseFloat(parts[0]);
                const lon = parseFloat(parts[1]);
                // return as [lon, lat] for rings/paths
                return [lon, lat];
            });

            if (pts.length === 0) return null;
            if (pts.length === 1) {
                return new Graphic({
                    geometry: new Point({ latitude: pts[0][1], longitude: pts[0][0], spatialReference: { wkid: 4326 } }),
                    symbol: { type: "simple-marker", color: "red" }
                });
            }

            // check if polygon (first equals last)
            const first = pts[0], last = pts[pts.length - 1];
            const isClosed = first[0] === last[0] && first[1] === last[1];

            if (isClosed) {
                return new Graphic({
                    geometry: new Polygon({ rings: [pts], spatialReference: { wkid: 4326 } }),
                    symbol: {
                        type: "simple-fill",
                        color: [0, 0, 255, 0.3],
                        outline: { color: "blue", width: 2 }
                    }
                });
            } else {
                return new Graphic({
                    geometry: new Polyline({ paths: [pts], spatialReference: { wkid: 4326 } }),
                    symbol: { type: "simple-line", color: "red", width: 2 }
                });
            }
        }

        // LOAD existing geometry into graphicsLayer (called when modal is shown and view ready)
        window.loadExistingGeometry = function () {
            graphicsLayer.removeAll();

            const txt = document.getElementById("Location").value;
            if (!txt || !txt.trim()) return;

            const gr = buildGraphicFromText(txt);
            if (!gr) return;

            graphicsLayer.add(gr);

            // ensure view is ready then goTo and enable editing
            view.when().then(function () {
                view.goTo(gr).catch(function (err) { console.warn("goTo error", err); });
                // update sketch with the graphic so user can edit it
                sketch.update([gr]);
            });
        };

        // ------------------ BOOTSTRAP MODAL HOOKS ------------------
        // When modal is shown we attach the view to the DOM and load geometry
        var modalEl = document.getElementById("mapModal");
        modalEl.addEventListener('shown.bs.modal', function () {
            // Attach view to DOM
            if (view && view.container !== "viewDiv") {
                view.container = "viewDiv";
            }

            // add UI widgets (only once)
            // measurement and sketch UI will be present because they reference view
            // if you want to ensure the widgets appear in the view UI:
            view.ui.add(sketch, "top-right");
            view.ui.add(measurement, "bottom-right");

            // Wait for view to be ready then load geometry
            view.when().then(function () {
                // small timeout to allow CSS animation to complete and layout stabilized
                setTimeout(function () {
                    loadExistingGeometry();
                }, 250);
            });
        });

        // When modal is hidden detach the view to avoid DOM/layout issues on subsequent opens
        modalEl.addEventListener('hidden.bs.modal', function () {
            // detach the view from DOM (keeps the view object alive)
            if (view && view.container) {
                view.container = null;
            }
        });

        // OPTIONAL: If you also want to open the map by clicking the textbox:
        document.getElementById("Location").addEventListener("click", function () {
            var modal = new bootstrap.Modal(modalEl);
            modal.show();
        });

    }); // end require
</script>




<script>
    var view, graphicsLayer, sketch;

    // ------------------ OPEN MODAL ------------------
    document.getElementById("Location").addEventListener("click", function () {

        var modal = new bootstrap.Modal(document.getElementById("mapModal"));
        modal.show();

        // IMPORTANT: Avoid using view.container again (breaks map)
        setTimeout(function () {
            if (view) {
                view.resize();          // correct way
                loadExistingGeometry(); // load existing polygon/line/point
            }
        }, 400);
    });


    // ------------------ ARCGIS SETUP ------------------
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
    ], function (
        Map, MapView, SketchClass, GraphicsLayerClass,
        Graphic, Polygon, Polyline, Point, Measurement
    ) {

        graphicsLayer = new GraphicsLayerClass();

        const map = new Map({
            basemap: "satellite",
            layers: [graphicsLayer]
        });

        // Create map only once
        view = new MapView({
            container: "viewDiv",
            map: map,
            center: [86.182457, 22.804294],
            zoom: 14
        });

        // Sketch tool
        sketch = new SketchClass({
            view: view,
            layer: graphicsLayer,
            creationMode: "update"
        });

        view.ui.add(sketch, "top-right");

        // Save when updated
        sketch.on("update", function (event) {
            if (event.state === "complete") {
                saveCoordinates(event.graphics[0].geometry);
            }
        });

        // Save when created
        sketch.on("create", function (event) {
            if (event.state === "complete") {
                saveCoordinates(event.graphic.geometry);
            }
        });

        // Measurement
        const measurement = new Measurement({ view: view });
        view.ui.add(measurement, "bottom-right");

        // Basemap change
        document.getElementById("basemapSelect").addEventListener("change", function () {
            map.basemap = this.value;
        });


        // ------------------ SAVE COORDINATES ------------------
        function saveCoordinates(geometry) {
            let coords = "";

            if (geometry.type === "point") {
                coords = geometry.latitude + "," + geometry.longitude;
            }

            if (geometry.type === "polyline") {
                coords = geometry.paths[0]
                    .map(p => `${p[1]},${p[0]}`)
                    .join("; ");
            }

            if (geometry.type === "polygon") {
                coords = geometry.rings[0]
                    .map(p => `${p[1]},${p[0]}`)
                    .join("; ");
            }

            console.log("Saved Coordinates:", coords);
            document.getElementById("Location").value = coords;
        }


        // ------------------ LOAD EXISTING SHAPE ------------------
        window.loadExistingGeometry = function () {

            graphicsLayer.removeAll();

            let value = document.getElementById("Location").value.trim();
            if (value === "") return;

            let coordPairs = value.split(";").map(v => v.trim());

            let points = coordPairs.map(c => {
                let parts = c.split(",");
                return [parseFloat(parts[1]), parseFloat(parts[0])]; // lng,lat
            });

            let graphic;

            // POINT
            if (points.length === 1) {
                graphic = new Graphic({
                    geometry: new Point({
                        latitude: points[0][1],
                        longitude: points[0][0]
                    }),
                    symbol: { type: "simple-marker", color: "red" }
                });
            }
            // POLYGON (first = last)
            else if (
                points.length > 1 &&
                points[0][0] === points[points.length - 1][0] &&
                points[0][1] === points[points.length - 1][1]
            ) {
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
            // POLYLINE
            else {
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

            // Enable editing automatically
            sketch.update([graphic]);
        };
    });
</script>




make changes to this code   
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
