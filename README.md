                                <div class="modal fade" id="mapModal" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog modal-xl">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title">Select Location</h5>
               <button type="button" class="close" data-dismiss="modal">&times;</button>
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


                              <div class="form-group col-md-3 mb-1">
    <label for="Location" class="m-0 mr-2 p-0 col-form-label-sm font-weight-bold fs-6">
        Location:<span class="text-danger">*</span>
    </label>
    <asp:TextBox ID="Location" ClientIDMode="Static" runat="server" CssClass="form-control form-control-sm col-12 input" />
</div>


existing data fetching  :
<input name="ctl00$MainContent$Road_Record$ctl02$Location" type="text" value="22.80474894876291,86.17889502642825; 22.804961587337772,86.17947438357555; 22.804452243220236,86.1796782314607; 22.80430883530968,86.17908814547734; 22.80474894876291,86.17889502642825" id="MainContent_Road_Record_Location_0" class="form-control form-control-sm col-12 input">


now i want to draw the coordinates for existing data when user clicks on Location textbox Map automatically draw that part with coordinates 
