```
<button class="btn btn-sm btn-light w-100" id="btnClearMap">Clear Map</button>
```

```
        <div class="modal-header">
            <span class="modal-title">📍 Location Preview</span>
            <button type="button" class="close" data-dismiss="modal">&times;</button>
        </div>

        <div class="modal-body p-0">
            <div id="mapHint">📌 Existing location preview</div>
            <div id="viewDiv"></div>
        </div>

    </div>
</div>
```
  

<style>
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

    #mapModal .modal-body {
        height: 70vh;
        position: relative;
        padding: 0;
    }

    #viewDiv {
        width: 100%;
        height: 100%;
    }

    #mapControls {
        background: #fff;
        padding: 6px;
        border-radius: 6px;
        width: 170px;
        box-shadow: 0 2px 6px rgba(0,0,0,.3);
    }

    #mapHint {
        position: absolute;
        bottom: 25px;
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

view.ui.add("mapControls", "top-left");

 
 
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


   
<div class="modal fade" id="mapModal" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog modal-xl modal-dialog-centered modal-dialog-scrollable">
        <div class="modal-content">

        <div class="modal-header">
            <span class="modal-title">📍 Select Location on Map</span>
            <button type="button" class="close" data-dismiss="modal">&times;</button>
        </div>

   
        <div class="modal-body p-1">                 

            <div id="mapHint">
                ✏️ Draw point / line / area to capture location
            </div>

      
            <div id="viewDiv"></div>
        </div>

    </div>
</div>

</div>

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
        bottom: 25px;
        left: 50%;
        transform: translateX(-50%);
        background: rgba(0,0,0,0.7);
        color: #fff;
        padding: 6px 14px;
        border-radius: 20px;
        font-size: 13px;
        z-index: 10;
    }
    #mapControls {
    background: #fff;
    padding: 6px;
    border-radius: 6px;
    width: 170px;
    box-shadow: 0 2px 6px rgba(0,0,0,.3);
}

</style>



              <div class="modal fade" id="mapModal" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog modal-xl"> 
        <div class="modal-content">
            <div class="modal-header">
                   <label class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6">Change Basemap:</label>
    <select id="basemapSelect" class="form-control form-control-sm" style="width:250px;margin-left:1%;">
        <option value="satellite">Satellite</option>
        <option value="topo-vector">Topographic</option>
        <option value="streets-vector">Streets</option>
        <option value="hybrid">Hybrid</option>
        <option value="dark-gray-vector">Dark Gray</option>
    </select>

                <button type="button" class="close" data-dismiss="modal">&times;</button>
            </div>

            <div class="modal-body">
                <div id="viewDiv" style="height:600px; width:100%;"></div>              
        </div>
    </div>
</div>
</div>

<script>
    let view, graphicsLayer;

    document.getElementById("Location").addEventListener("click", function () {

        var modal = new bootstrap.Modal(document.getElementById("mapModal"));
        modal.show();

        setTimeout(initMap, 300);
    });

    function initMap() {

        require([
            "esri/Map",
            "esri/views/MapView",
            "esri/Graphic",
            "esri/layers/GraphicsLayer",
            "esri/geometry/Polygon",
            "esri/geometry/Point"
        ], function (Map, MapView, Graphic, GraphicsLayer, Polygon, Point) {

            graphicsLayer = new GraphicsLayer();

            const map = new Map({
                basemap: document.getElementById("basemapSelect").value,
                layers: [graphicsLayer]
            });

            view = new MapView({
                container: "viewDiv",
                map: map,
                center: [86.18, 22.804],
                zoom: 17
            });

            drawExistingPolygon(Polygon, Graphic);

            document.getElementById("basemapSelect").addEventListener("change", function () {
                map.basemap = this.value;
            });
        });
    }

    function drawExistingPolygon(Polygon, Graphic) {

        let coordString = document.getElementById("Location").value;

        if (!coordString || coordString.trim() === "") return;

        let coords = coordString.split(";").map(p => {
            let [lat, lon] = p.trim().split(",");
            return [parseFloat(lon), parseFloat(lat)];
        });

        if (coords.length > 2) {
            let first = coords[0];
            let last = coords[coords.length - 1];
            if (first[0] !== last[0] || first[1] !== last[1]) {
                coords.push(first);
            }
        }

        let polygon = new Polygon({
            rings: [coords],
            spatialReference: { wkid: 4326 }
        });

        let symbol = {
            type: "simple-fill",
            color: [255, 0, 0, 0.3],
            outline: {
                color: [255, 0, 0],
                width: 2
            }
        };

        let graphic = new Graphic({
            geometry: polygon,
            symbol: symbol
        });

        graphicsLayer.add(graphic);

        view.when(() => {

            setTimeout(() => {
                view.goTo(graphic)
                    .catch(err => console.warn("GoTo failed:", err));
            }, 300);
        });
    }

</script>
