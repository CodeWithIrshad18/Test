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
