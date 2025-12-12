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
