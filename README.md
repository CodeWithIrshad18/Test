<script src="https://js.arcgis.com/4.30/"></script>
<link rel="stylesheet" href="https://js.arcgis.com/4.30/esri/themes/light/main.css">


@{
    ViewData["Title"] = "GISMap";
    Layout = "~/Views/Shared/_Layout1.cshtml";
}

<link rel="stylesheet" href="https://js.arcgis.com/4.30/esri/themes/light/main.css">

<div id="viewDiv" style="height:600px; width:100%;"></div>

<div style="margin-top:10px;">
    <label>Change Basemap:</label>
    <select id="basemapSelect" class="form-control" style="width:250px;">
        <option value="satellite">Satellite</option>
        <option value="topo-vector">Topographic</option>
        <option value="streets-vector">Streets</option>
        <option value="hybrid">Hybrid</option>
        <option value="dark-gray-vector">Dark Gray</option>
    </select>
</div>

<script src="https://js.arcgis.com/4.30/"></script>

<script>
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

        const view = new MapView({
            container: "viewDiv",
            map: map,
            center: [86.182457, 22.804294],
            zoom: 10
        });

        const sketch = new Sketch({
            view: view,
            layer: graphicsLayer,
            creationMode: "update"
        });
        view.ui.add(sketch, "top-right");

        const measurement = new Measurement({
            view: view
        });
        view.ui.add(measurement, "bottom-right");

        document.getElementById("basemapSelect").addEventListener("change", function () {
            map.basemap = this.value;
        });
    });
</script>



error 1 :


4.30/:31 Error: multipleDefine
    at h (4.30/:6:46)
    at ib (4.30/:28:1)
    at db (4.30/:31:117)
    at jquery.min.js:2:87230
    at jquery.min.js:2:220
    at jquery.min.js:2:225

error 2 : 

4.30/:31 Error: multipleDefine
    at h (4.30/:6:46)
    at ib (4.30/:28:1)
    at 4.30/:28:486
    at q (4.30/:5:455)
    at pb (4.30/:28:462)
    at Ma (4.30/:26:41)
    at HTMLScriptElement.<anonymous> (4.30/:30:106)

error 3: 
4.30/:24 Uncaught TypeError: Failed to execute 'appendChild' on 'Node': parameter 1 is not of type 'Node'.


Js : 
<script>
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

   
        const view = new MapView({
            container: "viewDiv",
            map: map,
            center: [86.182457, 22.804294], // Example: KMPM (lng, lat)
            zoom: 10
        });

        // Drawing Tools (Line & Polygon)
        const sketch = new Sketch({
            view: view,
            layer: graphicsLayer,
            creationMode: "update"
        });

        view.ui.add(sketch, "top-right");

        // Measurement widget
        const measurement = new Measurement({
            view: view
        });
        view.ui.add(measurement, "bottom-right");

        // Basemap Change Event
        document.getElementById("basemapSelect").addEventListener("change", function () {
            map.basemap = this.value;
        });
    });
</script>

viewside : 

@{
    ViewData["Title"] = "GISMap";
    Layout = "~/Views/Shared/_Layout1.cshtml";
}

<link rel="stylesheet" href="https://js.arcgis.com/4.30/esri/themes/light/main.css">

<div id="viewDiv" style="height:600px; width:100%;"></div>

<div style="margin-top:10px;">
    <label>Change Basemap:</label>
    <select id="basemapSelect" class="form-control" style="width:250px;">
        <option value="satellite">Satellite</option>
        <option value="topo-vector">Topographic</option>
        <option value="streets-vector">Streets</option>
        <option value="hybrid">Hybrid</option>
        <option value="dark-gray-vector">Dark Gray</option>
    </select>
</div>


<script src="https://js.arcgis.com/4.30/"></script>
