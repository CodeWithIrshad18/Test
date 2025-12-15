```
        <div class="modal-header">
            <span class="modal-title">📍 Location Preview</span>
            <button type="button" class="close" data-dismiss="modal">&times;</button>
        </div>

        <div class="modal-body">
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
        color: white;
        padding: 8px 16px;
    }

    #mapModal .modal-body {
        padding: 0;
        height: 70vh;
    }

    #viewDiv {
        width: 100%;
        height: 100%;
    }

    #mapControls {
        background: white;
        padding: 6px;
        border-radius: 6px;
        width: 170px;
        box-shadow: 0 2px 6px rgba(0,0,0,.3);
    }
</style>

<script src="https://js.arcgis.com/4.30/"></script>
<script>
    let view, graphicsLayer;

    document.getElementById("Location").addEventListener("click", function () {
        const modalEl = document.getElementById("mapModal");
        const modal = new bootstrap.Modal(modalEl);
        modal.show();

        modalEl.addEventListener("shown.bs.modal", function () {
            if (!view) {
                initMap();
            } else {
                view.container = "viewDiv";
                view.resize();
            }
        }, { once: true });
    });

    function initMap() {

        require([
            "esri/Map",
            "esri/views/MapView",
            "esri/Graphic",
            "esri/layers/GraphicsLayer",
            "esri/geometry/Point",
            "esri/geometry/Polyline",
            "esri/geometry/Polygon"
        ], function (
            Map, MapView, Graphic, GraphicsLayer,
            Point, Polyline, Polygon
        ) {

            graphicsLayer = new GraphicsLayer();

            const map = new Map({
                basemap: "satellite",
                layers: [graphicsLayer]
            });

            view = new MapView({
                container: "viewDiv",
                map: map,
                center: [86.18, 22.804],
                zoom: 17
            });

            view.ui.add("mapControls", "top-left");

            document.getElementById("basemapSelect").addEventListener("change", function () {
                map.basemap = this.value;
            });

            drawExistingGeometry(Graphic, Point, Polyline, Polygon);
        });
    }

    function drawExistingGeometry(Graphic, Point, Polyline, Polygon) {

        graphicsLayer.removeAll();

        const coordString = document.getElementById("Location").value;
        if (!coordString) return;

        const coords = coordString.split(";").map(p => {
            const [lat, lon] = p.trim().split(",");
            return [parseFloat(lon), parseFloat(lat)];
        });

        let geometry;

        if (coords.length === 1) {
            geometry = new Point({
                longitude: coords[0][0],
                latitude: coords[0][1]
            });
        }
        else if (coords.length === 2) {
            geometry = new Polyline({
                paths: [coords]
            });
        }
        else {
            geometry = new Polygon({
                rings: [coords]
            });
        }

        const graphic = new Graphic({
            geometry: geometry,
            symbol: getSymbol(geometry.type)
        });

        graphicsLayer.add(graphic);

        view.when(() => view.goTo(graphic));
    }

    function getSymbol(type) {
        if (type === "point") {
            return { type: "simple-marker", color: "red", size: 8 };
        }
        if (type === "polyline") {
            return { type: "simple-line", color: "red", width: 3 };
        }
        return {
            type: "simple-fill",
            color: [255, 0, 0, 0.3],
            outline: { color: "red", width: 2 }
        };
    }
</script>

