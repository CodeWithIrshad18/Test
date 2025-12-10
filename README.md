<!-- MAP MODAL -->
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

<script>
    document.getElementById("<%= Location.ClientID %>").addEventListener("click", function () {
        var modal = new bootstrap.Modal(document.getElementById("mapModal"));
        modal.show();

        setTimeout(function () {
            if (view) {
                view.resize(); 
            }
        }, 400);
    });
</script>

 <link rel="stylesheet" href="https://js.arcgis.com/4.30/esri/themes/light/main.css">
<script src="https://js.arcgis.com/4.30/"></script>

<script>
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
            zoom: 15
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

 
 
 <div class="form-group col-md-3 mb-1">
     <label for="Location" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Location:<span class="text-danger">*</span></label>
     <asp:TextBox ID="Location" runat="server" CssClass="form-control form-control-sm col-12 input"  />
      <asp:CustomValidator ID="CustomValidator4" runat="server" ClientValidationFunction="ValidateWithZero" ValidationGroup="submit" ControlToValidate="Location" ValidateEmptyText="true"></asp:CustomValidator>
 </div> 


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
            zoom: 15
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
