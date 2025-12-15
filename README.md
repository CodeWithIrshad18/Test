require([
    "esri/Map",
    "esri/views/MapView",
    "esri/Graphic",
    "esri/layers/GraphicsLayer",
    "esri/geometry/Polygon",
    "esri/geometry/Polyline",
    "esri/geometry/Point"
], function (Map, MapView, Graphic, GraphicsLayer, Polygon, Polyline, Point) {


function drawExistingGeometry(Graphic, Point, Polyline, Polygon) {

    let coordString = document.getElementById("Location").value;
    if (!coordString || coordString.trim() === "") return;

    let coords = coordString.split(";").map(p => {
        let [lat, lon] = p.trim().split(",");
        return [parseFloat(lon), parseFloat(lat)];
    });

    let geometry, symbol;

    /* POINT */
    if (coords.length === 1) {
        geometry = new Point({
            longitude: coords[0][0],
            latitude: coords[0][1]
        });

        symbol = {
            type: "simple-marker",
            color: "red",
            size: 8
        };
    }

    /* POLYLINE */
    else if (coords.length === 2) {
        geometry = new Polyline({
            paths: [coords],
            spatialReference: { wkid: 4326 }
        });

        symbol = {
            type: "simple-line",
            color: "red",
            width: 3
        };
    }

    /* POLYGON */
    else {
        let first = coords[0];
        let last = coords[coords.length - 1];
        if (first[0] !== last[0] || first[1] !== last[1]) {
            coords.push(first);
        }

        geometry = new Polygon({
            rings: [coords],
            spatialReference: { wkid: 4326 }
        });

        symbol = {
            type: "simple-fill",
            color: [255, 0, 0, 0.3],
            outline: { color: "red", width: 2 }
        };
    }

    let graphic = new Graphic({
        geometry: geometry,
        symbol: symbol
    });

    graphicsLayer.add(graphic);

    view.when(() => {
        view.goTo(graphic).catch(err => console.warn(err));
    });
}

drawExistingGeometry(Graphic, Point, Polyline, Polygon);



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
