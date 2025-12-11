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

    const measurement = new Measurement({
        view: view
    });
    view.ui.add(measurement, "bottom-right");

    document.getElementById("basemapSelect").addEventListener("change", function () {
        map.basemap = this.value;
    });
});





modal close button is not working 

<div class="modal fade" id="mapModal" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog modal-xl"> 
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title">Select Location</h5>
                <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
            </div>

            <div class="modal-body">
                <div id="viewDiv" style="height:600px; width:100%;"></div>

                <div class="mt-2">
                    <label>Change Basemap:</label>
                    <select id="basemapSelect" class="form-control" style="width:250px;">
                        <option value="satellite">Satellite</option>
                        <option value="topo-vector">Topographic</option>
                        <option value="streets-vector">Streets</option>
                        <option value="hybrid">Hybrid</option>
                        <option value="dark-gray-vector">Dark Gray</option>
                    </select>
                </div>
            </div>
        </div>
    </div>
</div>


and this error is showing 

Road_side_barricade_Entry.aspx:1219 Uncaught TypeError: view.resize is not a function
    at Road_side_barricade_Entry.aspx:1219:27

  <script>
      document.getElementById("Location").addEventListener("click", function () {
          var modal = new bootstrap.Modal(document.getElementById("mapModal"));
          modal.show();

          setTimeout(function () {
              if (view) {
                  view.resize();
              }
          }, 400);
      });


      var view; // global so modal click can access it

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

          const measurement = new Measurement({
              view: view
          });
          view.ui.add(measurement, "bottom-right");

          document.getElementById("basemapSelect").addEventListener("change", function () {
              map.basemap = this.value;
          });
      });
  </script>
