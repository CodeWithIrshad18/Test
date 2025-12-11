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
