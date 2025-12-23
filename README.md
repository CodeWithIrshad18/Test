 <script>

     document.getElementById("Location").addEventListener("click", function () {
         var modal = new bootstrap.Modal(document.getElementById("mapModal"));
         modal.show();

         setTimeout(function () {
             if (view) {
                 view.container = "viewDiv";   
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
             center: [86.202777, 22.797370],
             zoom: 13
         });


         view.when(() => {

 
             view.ui.add("mapControls", "top-left");

             document.getElementById("basemapSelect").addEventListener("change", function () {
                 map.basemap = this.value;
             });

   
             document.getElementById("btnClearMap").addEventListener("click", function () {
                 graphicsLayer.removeAll();
                 document.getElementById("Location").value = "";
             });
         });


         const sketch = new Sketch({
             view: view,
             layer: graphicsLayer,
             creationMode: "update"
         });
         view.ui.add(sketch, "top-right");


         const measurement = new Measurement({ view });
         view.ui.add(measurement, "bottom-right");


         view.ui.add("mapControls", "top-left");


         document.getElementById("btnClearMap").addEventListener("click", function () {
             graphicsLayer.removeAll();
             document.getElementById("Location").value = "";
         });

  
         document.getElementById("basemapSelect").addEventListener("change", function () {
             map.basemap = this.value;
         });





         function getLatLongCoords(geometry) {
             let geom = webMercatorUtils.webMercatorToGeographic(geometry);

             if (geom.type === "point") {
                 return geom.latitude + "," + geom.longitude;
             }

             if (geom.type === "polyline") {
                 return geom.paths[0]
                     .map(p => p[1] + "," + p[0])  
                     .join("; ");
             }

             if (geom.type === "polygon") {
                 return geom.rings[0]
                     .map(p => p[1] + "," + p[0])  
                     .join("; ");
             }
         }

         sketch.on("create", function (event) {
             if (event.state === "complete") {
                 let coords = getLatLongCoords(event.graphic.geometry);
                 document.getElementById("Location").value = coords;
             }
         });

         sketch.on("update", function (event) {
             if (event.graphics.length > 0) {

                 let geometry = event.graphics[0].geometry;

                 let coords = getLatLongCoords(geometry);

                 document.getElementById("Location").value = coords;

               
             }
         });

     });
     function clearGraphics() {
         view.graphics.removeAll();
         document.getElementById("Location").value = "";
     }
 </script>


<div class="form-group col-md-3 mb-1">
    <label for="Location" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Location:<span class="text-danger">*</span></label>
    <asp:TextBox ID="Location" runat="server" ClientIDMode="Static"  CssClass="form-control form-control-sm col-12 input"  autocomplete="off"/>
     <asp:CustomValidator ID="CustomValidator4" runat="server" ClientValidationFunction="ValidateWithZero" ValidationGroup="submit" ControlToValidate="Location" ValidateEmptyText="true" ></asp:CustomValidator>
</div> 
