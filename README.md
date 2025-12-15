<div id="mapControls">
    <select id="basemapSelect" class="form-control form-control-sm mb-1">
        <option value="satellite">Satellite</option>
        <option value="topo-vector">Topographic</option>
        <option value="streets-vector">Streets</option>
        <option value="hybrid">Hybrid</option>
        <option value="dark-gray-vector">Dark Gray</option>
    </select>

    <button class="btn btn-sm btn-light w-100" id="btnClearMap">
        Clear Map
    </button>
</div>

#mapControls {
    background: #fff;
    padding: 6px;
    border-radius: 6px;
    width: 170px;
    box-shadow: 0 2px 6px rgba(0,0,0,.3);
}

view = new MapView({
    container: "viewDiv",
    map: map,
    center: [86.182457, 22.804294],
    zoom: 14
});

/* Wait until view is ready */
view.when(() => {

    /* Add custom controls */
    view.ui.add("mapControls", "top-left");

    /* Basemap switch (FIXED) */
    document.getElementById("basemapSelect").addEventListener("change", function () {
        map.basemap = this.value;
    });

    /* Clear button (FIXED) */
    document.getElementById("btnClearMap").addEventListener("click", function () {
        graphicsLayer.removeAll();
        document.getElementById("Location").value = "";
    });
});

function clearGraphics() {
    graphicsLayer.removeAll();
    document.getElementById("Location").value = "";
}

    
    
    <div id="mapControls" style="display:none;">
    <select id="basemapSelect" class="form-control form-control-sm mb-1">
        <option value="satellite">Satellite</option>
        <option value="topo-vector">Topographic</option>
        <option value="streets-vector">Streets</option>
        <option value="hybrid">Hybrid</option>
        <option value="dark-gray-vector">Dark Gray</option>
    </select>

    <button class="btn btn-sm btn-light w-100" id="btnClearMap">Clear Map</button>
</div>

   
<div class="modal fade" id="mapModal" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog modal-xl modal-dialog-centered modal-dialog-scrollable">
        <div class="modal-content">

        <div class="modal-header">
            <span class="modal-title">📍 Select Location on Map</span>
            <button type="button" class="close" data-dismiss="modal">&times;</button>
        </div>

   
        <div class="modal-body">                 

            <div id="mapHint">
                ✏️ Draw point / line / area to capture location
            </div>

      
            <div id="viewDiv"></div>
        </div>

    </div>
</div>

</div>

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
             center: [86.182457, 22.804294],
             zoom: 14
         });

         /* Sketch widget */
         const sketch = new Sketch({
             view: view,
             layer: graphicsLayer,
             creationMode: "update"
         });
         view.ui.add(sketch, "top-right");

         /* Measurement */
         const measurement = new Measurement({ view });
         view.ui.add(measurement, "bottom-right");

         /* 🔥 ADD CUSTOM UI HERE */
         view.ui.add("mapControls", "top-left");

         /* Clear button */
         document.getElementById("btnClearMap").addEventListener("click", function () {
             graphicsLayer.removeAll();
             document.getElementById("Location").value = "";
         });

         /* Basemap switch */
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

   <style>
    /* Modal look */
    #mapModal .modal-content {
        border-radius: 10px;
        overflow: hidden;
    }

    #mapModal .modal-header {
        background: linear-gradient(90deg, #1e3c72, #2a5298);
        color: #fff;
        padding: 8px 16px;
    }

    #mapModal .modal-title {
        font-weight: 600;
        font-size: 16px;
    }

    #mapModal .close {
        color: #fff;
        opacity: 1;
    }

    #mapModal .modal-body {
        padding: 0;
        height: 70vh;
        position: relative;
    }

    #viewDiv {
        height: 100%;
        width: 100%;
    }

    /* Floating toolbar */
    #mapToolbar {
        position: absolute;
        top: 12px;
        left: 12px;
        z-index: 10;
        display: flex;
        gap: 8px;
        background: rgba(255, 255, 255, 0.95);
        padding: 6px 8px;
        border-radius: 6px;
        box-shadow: 0 4px 10px rgba(0,0,0,.2);
    }

    /* Bottom hint */
    #mapHint {
        position: absolute;
        bottom: 15px;
        left: 50%;
        transform: translateX(-50%);
        background: rgba(0,0,0,0.7);
        color: #fff;
        padding: 6px 14px;
        border-radius: 20px;
        font-size: 13px;
        z-index: 10;
    }
</style>

in this basemap dropdown is not working 
