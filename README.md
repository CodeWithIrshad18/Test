make the UI change to this code also and also add polyline also sketch also for existing data 
      
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


          <script src="https://js.arcgis.com/4.30/"></script>
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

