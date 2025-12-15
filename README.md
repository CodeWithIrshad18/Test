<!-- MAP MODAL -->

<div class="modal fade" id="mapModal" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog modal-xl modal-dialog-centered modal-dialog-scrollable">
        <div class="modal-content">

```
        <!-- HEADER -->
        <div class="modal-header">
            <span class="modal-title">📍 Select Location on Map</span>
            <button type="button" class="close" data-dismiss="modal">&times;</button>
        </div>

        <!-- BODY -->
        <div class="modal-body">

            <!-- FLOATING TOOLBAR -->
            <div id="mapToolbar">
                <select id="basemapSelect" class="form-control form-control-sm">
                    <option value="satellite">Satellite</option>
                    <option value="topo-vector">Topographic</option>
                    <option value="streets-vector">Streets</option>
                    <option value="hybrid">Hybrid</option>
                    <option value="dark-gray-vector">Dark Gray</option>
                </select>

                <button class="btn btn-sm btn-light" onclick="clearGraphics()">Clear</button>
            </div>

            <!-- HELP HINT -->
            <div id="mapHint">
                ✏️ Draw point / line / area to capture location
            </div>

            <!-- MAP -->
            <div id="viewDiv"></div>
        </div>

    </div>
</div>
```

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

function clearGraphics() {
    view.graphics.removeAll();
    document.getElementById("Location").value = "";
}



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

    #mapModal .modal-header label {
        margin: 0;
        font-weight: 600;
    }

    #mapModal .close {
        color: white;
        opacity: 1;
    }

    #mapModal .modal-body {
        padding: 0;
        height: 70vh;
    }

    #viewDiv {
        height: 100%;
        width: 100%;
    }
</style>

<div id="mapToolbar">
    <select id="basemapSelect" class="form-control form-control-sm">
        <option value="satellite">Satellite</option>
        <option value="topo-vector">Topographic</option>
        <option value="streets-vector">Streets</option>
        <option value="hybrid">Hybrid</option>
        <option value="dark-gray-vector">Dark Gray</option>
    </select>

    <button class="btn btn-sm btn-light" onclick="clearGraphics()">Clear</button>
</div>

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



</div> <!-- close card/body/etc -->

<!-- MAP MODAL (MOVE HERE) -->
<div class="modal fade" id="mapModal" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog modal-xl modal-dialog-centered modal-dialog-scrollable">
        <div class="modal-content">
            <div class="modal-header">
                <label class="font-weight-bold">Change Basemap:</label>
                <select id="basemapSelect" class="form-control form-control-sm ml-2" style="width:250px;">
                    <option value="satellite">Satellite</option>
                    <option value="topo-vector">Topographic</option>
                    <option value="streets-vector">Streets</option>
                    <option value="hybrid">Hybrid</option>
                    <option value="dark-gray-vector">Dark Gray</option>
                </select>

                <button type="button" class="close" data-dismiss="modal">&times;</button>
            </div>

            <div class="modal-body p-0">
                <div id="viewDiv" style="height:600px;width:100%;"></div>
            </div>
        </div>
    </div>
</div>




this is my full .aspx page. issue is here when i scroll down modal is not showing properly it shows on upper side so the UI is bad 

<asp:Content ID="Content2" ContentPlaceHolderID="MainContent" runat="server">
    <%--<asp:UpdatePanel ID="UpdatePanel1" runat="server">
        <ContentTemplate>--%>

            <div class="card m-2 shadow-lg">
                <div class="card-header bg-info text-light">
                    <h6 class="m-0">Road Side Barricade Entry</h6>
                </div>
                <div class="card-body pt-1">
                    <div>
                       <uc1:MyMsgBox ID="MyMsgBox" runat="server"/>
                    </div>
                     <div class="row m-0 justify-content-end" style="visibility:hidden">
                         <asp:Button ID="btnNew" runat="server"  Text="New" OnClick="btnNew_Click" CssClass="btn btn-primary btn-sm" />
                    </div>

                   <div id="div_gridrecords" runat="server" visible="false"> 
                    <fieldset class="" style="border:1px solid #bfbebe;padding:5px 20px 5px 20px;border-radius:6px">
                        <legend style="width:auto;border:0;font-size:14px;margin:0px 6px 0px 6px;padding:0px 5px 0px 5px;color:#0000FF"><b>Records</b></legend>

                        <div class="w-100 border">
                             <cc1:detailscontainer ID="Road_Records" runat="server" AutoGenerateColumns="False"
                                AllowPaging="True" CellPadding="4" GridLines="None" Width="100%" DataMember="App_Road_side_barricade_Entry"
                                DataKeyNames="ID" DataSource="<%# PageRecordsDataSet %>"
                                ForeColor="#333333" ShowHeaderWhenEmpty="True" HeaderStyle-Font-Size="Smaller" RowStyle-Font-Size="Smaller" OnPageIndexChanging="Road_Records_PageIndexChanging"
                                OnSelectedIndexChanged="Road_Records_SelectedIndexChanged" OnRowDataBound="Road_Records_RowDataBound" >
                                <AlternatingRowStyle BackColor="White" ForeColor="#284775" />
                                <Columns>
                                <asp:TemplateField HeaderText="ID" SortExpression="ID" Visible="False">
                                <ItemTemplate>
                                <asp:Label ID="ID" runat="server"></asp:Label>
                                </ItemTemplate>
                                </asp:TemplateField>
                                <asp:TemplateField HeaderText="Pno" 
                                SortExpression="Pno">
                                <ItemTemplate>
                                <asp:LinkButton ID="LinkButton1" runat="server" CommandName="select" Text='<%# Bind("ReqNo") %>' ForeColor="Blue" Font-Bold="true"></asp:LinkButton>
                                </ItemTemplate>
                                <HeaderStyle HorizontalAlign="Left" />
                                    
                                </asp:TemplateField>
                                    <asp:BoundField DataField="Pno" HeaderText="Pno" 
                                                                        SortExpression="Pno" HeaderStyle-HorizontalAlign="Left" 
                                                                        ItemStyle-HorizontalAlign="Left">
                                                                    <HeaderStyle HorizontalAlign="Left" />
                                                                    <ItemStyle HorizontalAlign="Left" />
                                                                </asp:BoundField>
                                    <asp:BoundField DataField="Name" HeaderText="Name" 
                                                                        SortExpression="Name" HeaderStyle-HorizontalAlign="Left" 
                                                                        ItemStyle-HorizontalAlign="Left">
                                                                    <HeaderStyle HorizontalAlign="Left" />
                                                                    <ItemStyle HorizontalAlign="Left" />
                                                                </asp:BoundField>
                                 <asp:BoundField DataField="Department" HeaderText="Department" 
                                                                        SortExpression="department" HeaderStyle-HorizontalAlign="Left" 
                                                                        ItemStyle-HorizontalAlign="Left">
                                                                    <HeaderStyle HorizontalAlign="Left" />
                                                                    <ItemStyle HorizontalAlign="Left" />
                                                                </asp:BoundField> 
                                    <asp:BoundField DataField="Chainage" HeaderText="Chainage" 
                                                                        SortExpression="dept" HeaderStyle-HorizontalAlign="Left" 
                                                                        ItemStyle-HorizontalAlign="Left">
                                                                    <HeaderStyle HorizontalAlign="Left" />
                                                                    <ItemStyle HorizontalAlign="Left" />
                                                                </asp:BoundField> 
                                    <asp:BoundField DataField="Start_Date" HeaderText="Start Date" 
                                                                        SortExpression="Start_Date" HeaderStyle-HorizontalAlign="Left" 
                                                                        ItemStyle-HorizontalAlign="Left">
                                                                    <HeaderStyle HorizontalAlign="Left" />
                                                                    <ItemStyle HorizontalAlign="Left" />
                                                                </asp:BoundField> 
                                    <asp:BoundField DataField="End_Date" HeaderText="End Date" 
                                                                        SortExpression="Date" HeaderStyle-HorizontalAlign="Left" 
                                                                        ItemStyle-HorizontalAlign="Left">
                                                                    <HeaderStyle HorizontalAlign="Left" />
                                                                    <ItemStyle HorizontalAlign="Left" />
                                                                </asp:BoundField>
                                    <asp:BoundField DataField="Status" HeaderText="Status" 
                                                                        HeaderStyle-HorizontalAlign="Left" 
                                                                        ItemStyle-HorizontalAlign="Left">
                                                                    <HeaderStyle HorizontalAlign="Left" />
                                                                    <ItemStyle HorizontalAlign="Left" />
                                                                </asp:BoundField>
                              
                                </Columns>
                                <EditRowStyle BackColor="#999999" />
                                <FooterStyle BackColor="#5D7B9D" ForeColor="White" Font-Bold="True" />
                                <HeaderStyle BackColor="#5D7B9D" Font-Bold="True" ForeColor="White" />
                                <PagerSettings Mode="Numeric" />
                                <PagerStyle BackColor="#284775" ForeColor="White" HorizontalAlign="Center" />
                                <RowStyle BackColor="#F7F6F3" ForeColor="#333333" />
                                <SelectedRowStyle BackColor="#E2DED6" Font-Bold="False" ForeColor="#333333" />
                                <SortedAscendingCellStyle BackColor="#E9E7E2" />
                                <SortedAscendingHeaderStyle BackColor="#506C8C" />
                                <SortedDescendingCellStyle BackColor="#FFFDF8" />
                                <SortedDescendingHeaderStyle BackColor="#6F8DAE" />
                              </cc1:detailscontainer>
                        </div>
                    </fieldset>
                  </div>
 <div id="div_details" runat="server">
                    <%--<fieldset class="" style="border:1px solid #bfbebe;padding:5px 20px 5px 20px;border-radius:6px">
                        <legend style="width:auto;border:0;font-size:14px;margin:0px 6px 0px 6px;padding:0px 5px 0px 5px;color:#0000FF"><b>Record Details</b></legend>--%>

                         <cc1:formcointainer ID="Road_Record" runat="server" AllowPaging="True" AutoGenerateColumns="False"
                            PageSize="1" ShowHeader="False" Width="100%" DataSource="<%# PageRecordDataSet %>"
                            DataMember="App_Road_side_barricade_Entry" DataKeyNames="ID" OnNewRowCreatingEvent="Road_Record_NewRowCreatingEvent" OnRowDataBound="Road_Record_RowDataBound"
                             BindingErrorMessage="xxxxx" GridLines="None">
                            <Columns>
                            <asp:TemplateField SortExpression="ID" Visible="False">
                                <ItemTemplate>
                                <asp:Label ID="ID" runat="server" ></asp:Label>
                                </ItemTemplate>
                                </asp:TemplateField>
                            <asp:TemplateField>
                            <ItemTemplate>
                                 <fieldset class="" style="border:1px solid #bfbebe;padding:5px 20px 5px 20px;border-radius:6px">
                        <legend style="width:auto;border:0;font-size:14px;margin:0px 6px 0px 6px;padding:0px 5px 0px 5px;color:#0000FF"><b>User Details</b></legend>
                            <div class="row">
                                <div class="form-group col-md-2 mb-1">
                                    <label for="Pno" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Pno:<span class="text-danger">*</span></label>
                                    <asp:TextBox ID="Pno" runat="server" CssClass="form-control form-control-sm col-12 input" Enabled="false"  />
                                    </div> 
                                <div class="form-group col-md-3 mb-1">
                                    <label for="Name" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Name:<span class="text-danger">*</span></label>
                                    <asp:TextBox ID="Name" runat="server" CssClass="form-control form-control-sm col-12 input" Enabled="false"  />
                                </div>
                                <div class="form-group col-md-4 mb-1">
                                    <label for="Department" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Department:<span class="text-danger">*</span></label>
                                    <asp:TextBox ID="Department" runat="server" CssClass="form-control form-control-sm col-12 input" Enabled="false"  />
                                 </div>
                                <div class="form-group col-md-3 mb-1">
                                    <label for="Designation" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Designation:<span class="text-danger">*</span></label>
                                    <asp:TextBox ID="Designation" runat="server" CssClass="form-control form-control-sm col-12 input" Enabled="false"  />
                                 </div>
                             </div>
                             <div class="row">
                                <div class="form-group col-md-2 mb-1">
                                    <label for="level" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Level:<span class="text-danger">*</span></label>
                                    <asp:TextBox ID="Level" runat="server" CssClass="form-control form-control-sm col-12 input" Enabled="false"  />
                                 </div>
                                <div class="form-group col-md-4 mb-1">
                                    <label for="EmailID" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >EmailID:<span class="text-danger">*</span></label>
                                    <asp:TextBox ID="EmailID" runat="server" CssClass="form-control form-control-sm col-12 input" Enabled="false"  />
                                 </div>
                                
                                
                            </div>
                                     </fieldset>
                                
                                 <fieldset class="" style="border:1px solid #bfbebe;padding:5px 20px 5px 20px;border-radius:6px">
                        <legend style="width:auto;border:0;font-size:14px;margin:0px 6px 0px 6px;padding:0px 5px 0px 5px;color:#0000FF"><b>Information</b></legend>
                            <div class="row">
                                        <div class="form-group col-md-4 mb-1">
                                    <label for="DivisionID" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Division<span class="text-danger">*</span></label>
                                                       <asp:DropDownList ID="Info_DivisionID" runat="server" DataSource="<%# PageDDLDataset %>" DataMember="Division" DataTextField="Division" DataValueField="ID" AutoPostBack="true" CssClass="form-control form-control-sm col-sm-12 col-12 input" OnSelectedIndexChanged="Info_DivisionID_SelectedIndexChanged"   />
                                                         <asp:CustomValidator ID="CustomValidator3" runat="server" ClientValidationFunction="ValidateWithZero" ValidationGroup="submit" ControlToValidate="Info_DivisionID" ValidateEmptyText="true"></asp:CustomValidator>
                                </div>
                                <div class="form-group col-md-4 mb-1">
                                    <label for="DepartmentID" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Department<span class="text-danger">*</span></label>
                                                        <asp:DropDownList ID="Info_DepartmentID" runat="server" DataSource="<%# PageDDLDataset %>" DataMember="Department" DataTextField="Department" DataValueField="ID" AutoPostBack="true" CssClass="form-control form-control-sm col-sm-12 col-12 input" OnSelectedIndexChanged="Info_DepartmentID_SelectedIndexChanged"  />
                                                        <asp:CustomValidator ID="CustomValidator1" runat="server" ClientValidationFunction="ValidateWithZero" ValidationGroup="submit" ControlToValidate="Info_DepartmentID" ValidateEmptyText="true"></asp:CustomValidator>
                                </div>
                                <div class="form-group col-md-4 mb-1">
                                    <label for="Info_SectionID" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Section<span class="text-danger">*</span></label>
                                                        <asp:DropDownList ID="Info_SectionID" runat="server" DataSource="<%# PageDDLDataset %>" DataMember="Section" DataTextField="Section" DataValueField="ID" CssClass="form-control form-control-sm col-sm-12 col-12 input"  />
                                                        <asp:CustomValidator ID="CustomValidator2" runat="server" ClientValidationFunction="ValidateWithZero" ValidationGroup="submit" ControlToValidate="Info_SectionID" ValidateEmptyText="true"></asp:CustomValidator>
                                </div>
                                    
                            </div>
                            <div class="row">
                                <div class="form-group col-md-3 mb-1">
                                    <label for="Location" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Location:<span class="text-danger">*</span></label>
                                    <asp:TextBox ID="Location" runat="server" ClientIDMode="Static"  CssClass="form-control form-control-sm col-12 input"  autocomplete="off"/>
                                     <asp:CustomValidator ID="CustomValidator4" runat="server" ClientValidationFunction="ValidateWithZero" ValidationGroup="submit" ControlToValidate="Location" ValidateEmptyText="true" ></asp:CustomValidator>
                                </div> 

                                                        <!-- MAP MODAL -->


                                <div class="form-group col-md-3 mb-1">
                                    <label for="Chainage" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Chainage:<span class="text-danger">*</span></label>
                                    <asp:TextBox ID="Chainage" runat="server" CssClass="form-control form-control-sm col-12 input"   />
                                    <asp:CustomValidator ID="CustomValidator9" runat="server" ClientValidationFunction="ValidateWithZero" ValidationGroup="submit" ControlToValidate="Chainage" ValidateEmptyText="true"></asp:CustomValidator>
                                </div>
                                <div class="form-group col-md-3 mb-1">
                                    <label for="Department" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Length of Barricade:<span class="text-danger">*</span></label>
                                    <asp:TextBox ID="Length_of_barricade" runat="server" CssClass="form-control form-control-sm col-12 input"   />
                                     <asp:CustomValidator ID="CustomValidator5" runat="server" ClientValidationFunction="ValidateWithZero" ValidationGroup="submit" ControlToValidate="Length_of_barricade" ValidateEmptyText="true"></asp:CustomValidator>
                                 </div>
                                <div class="form-group col-md-3 mb-1">
                                    <label for="GIS Number" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >GIS Number:<span class="text-danger">*</span></label>
                                    <asp:TextBox ID="GISNo" runat="server" CssClass="form-control form-control-sm col-12 input"  />
                                     <asp:CustomValidator ID="CustomValidator6" runat="server" ClientValidationFunction="ValidateWithZero" ValidationGroup="submit" ControlToValidate="GISNo" ValidateEmptyText="true"></asp:CustomValidator>
                                 </div>
                            </div>
                            <div class="row">
                               <div class="form-group col-lg-3 col-md-4 mb-1">
                                                      <label for="CompanyID" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Working Agency Code:<span class="text-danger">*</span></label>
                                                      <div class="VendorColumn">
                                                          <button class="btn btn-sm btn-default selectArea w-100" type="button"  onclick="togglefloatDiv(this);" id="btn-dwn">
                                                             <span id="filter-option" class="filter-option float-left">No item Selected</span>
                                                             <span class="caret" id="caret"></span>
                                                          </button>
                                                          <div class="floatDiv" runat="server"  style="border:1px solid black;position:absolute;z-index:1000;box-shadow:0 6px 12px rgb(0 0 0 / 18%);max-height:200px;background-color:white;width:100%;padding:5px;display:none">
                                                             <asp:TextBox runat="server" ID="SearchObserver" CssClass="form-control form-control-sm" oninput="filterFunction(this);" AutoComplete="off" ViewStateMode="Disabled" placeholder="Enter VendorCode or Name" Font-Size="Smaller" />
                                                             <ul id="searchList" style="overflow:auto;max-height:150px;" class="searchList p-0"  >
                                                                 
                                                             </ul>
                                                              <asp:TextBox runat="server" ID="VendorCode" AutoPostBack="true" OnTextChanged="VendorName_TextChanged" Width="0%" style="display:none" />
                                                                  
                                                              
                                                         </div>
                                                         <%--<asp:RequiredFieldValidator ID="RequiredFieldValidator81" class="invalid-tooltip" ValidationGroup="save" runat="server" ErrorMessage="select vendor" ControlToValidate="VendorCode" ForeColor="White" Font-Size="X-Small"></asp:RequiredFieldValidator>--%>
                                                      </div>
                                                </div>
                                                <div class="form-group col-lg-4 col-md-4 mb-1">
                                                    <label for="DivisionID" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Working Agency Name:<span class="text-danger">*</span></label>
                                                    <asp:TextBox ID="VendorName" runat="server" CssClass="form-control form-control-sm col-sm-12 col-12" Enabled="false" Font-Size="Smaller"   />
                                                    <%--<asp:RequiredFieldValidator ID="RequiredFieldValidator21" class="invalid-tooltip" ValidationGroup="save" runat="server" ErrorMessage="Vendorcode Required" ControlToValidate="VendorName" ForeColor="White" Font-Size="X-Small"></asp:RequiredFieldValidator>--%>
                                                </div> 
                                <div class="form-group col-md-2 mb-1">
                                    <label for="Start_Date" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Start Date:<span class="text-danger">*</span></label>
                                    <asp:TextBox ID="Start_Date" runat="server" CssClass="form-control form-control-sm col-12 input"  />
                                    <ask:CalendarExtender ID="CalendarExtender1" runat="server" Format="dd/MM/yyyy" PopupButtonID="ImageButton2" PopupPosition="BottomRight" TargetControlID="Start_Date" Enabled="true" />
                                    <asp:CustomValidator ID="CustomValidator7" runat="server" ClientValidationFunction="ValidateWithZero" ValidationGroup="submit" ControlToValidate="Start_Date" ValidateEmptyText="true"></asp:CustomValidator>
                                </div>
                                <div class="form-group col-md-2 mb-1">
                                    <label for="End_Date" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >End Date:<span class="text-danger">*</span></label>
                                    <asp:TextBox ID="End_Date" runat="server" CssClass="form-control form-control-sm col-12 input"   />
                                    <ask:CalendarExtender ID="CalendarExtender2" runat="server" Format="dd/MM/yyyy" PopupButtonID="ImageButton2" PopupPosition="BottomRight" TargetControlID="End_Date" Enabled="true" />
                                    <asp:CustomValidator ID="CustomValidator8" runat="server" ClientValidationFunction="ValidateWithZero" ValidationGroup="submit" ControlToValidate="End_Date" ValidateEmptyText="true"></asp:CustomValidator>
                                 </div>
                                
                            </div>
                           </fieldset>
                            </ItemTemplate>
                            </asp:TemplateField>
                                   
                            </Columns>
                            <PagerSettings Visible="False" />
                            </cc1:formcointainer>

                    <%--</fieldset>--%>
<%-------Checkpoint start-------%>
                    <fieldset class="" style="border:1px solid #bfbebe;padding:5px 20px 5px 20px;border-radius:6px">
                        <legend style="width:auto;border:0;font-size:14px;margin:0px 6px 0px 6px;padding:0px 5px 0px 5px;color:#0000FF"><b>Check point</b></legend>

<div class="ddlcontainer mt-2">
                              <cc1:detailscontainer ID="Checkpoint_Details" runat="server" AutoGenerateColumns="False"
                                        AllowPaging="True" CellPadding="4" GridLines="None" Width="100%" DataMember="App_Road_Side_Checkpoint_Details"
                                        DataKeyNames="ID" DataSource="<%# PageRecordDataSet %>"
                                        ForeColor="#333333" ShowHeaderWhenEmpty="False" OnRowDataBound="Checkpoint_Details_RowDataBound" 
                                        PageSize="10" PagerSettings-Visible="True" PagerStyle-HorizontalAlign="Center" OnPageIndexChanging="Checkpoint_Details_PageIndexChanging"
                                        PagerStyle-Wrap="True" HeaderStyle-Font-Size="Smaller" RowStyle-Font-Size="Smaller" OnDataRowAddingEvent="Checkpoint_Details_DataRowAddingEvent" CssClass="m-auto border border-info " HeaderStyle-HorizontalAlign="left" >
                                        <AlternatingRowStyle BackColor="White" ForeColor="#284775" />

                                        <Columns>
                                            <asp:TemplateField HeaderText="ID" SortExpression="ID" Visible="False">
                                                <ItemTemplate>
                                                    <asp:Label ID="ID" runat="server"></asp:Label>
                                                    <asp:Label ID="MasterID" runat="server"></asp:Label>
                                                    <asp:Label ID="ReqNo" runat="server"></asp:Label>
                                                </ItemTemplate>
                                            </asp:TemplateField>

                                            <asp:TemplateField HeaderText="SlNo" SortExpression="SlNo" HeaderStyle-Width="2%" >
                                                    <ItemTemplate>
                                                        <asp:Label ID="SlNo" runat="server"></asp:Label>
                                                    </ItemTemplate>
                                                </asp:TemplateField> 
                                            <asp:TemplateField HeaderText="Check point" SortExpression="Recommendations" HeaderStyle-Width="35%" >
                                                    <ItemTemplate>
                                                        <asp:Label ID="Checkpoints" runat="server" CssClass="col-sm-10 input pl-0"   />
                                                        
                                                    </ItemTemplate>
                                                </asp:TemplateField>
                                            
                                            <asp:TemplateField HeaderText="OK" HeaderStyle-Width="3%" >
                                                <ItemTemplate>
                                                    <asp:RadioButton ID="OK" runat="server" CssClass="radio" GroupName="checkpoint"  onclick="Checkpoint()" />
                                                </ItemTemplate>
                                            </asp:TemplateField>
                                           <asp:TemplateField HeaderText="Not OK" HeaderStyle-Width="5%" >
                                                <ItemTemplate>
                                                    <asp:RadioButton ID="Not_OK" runat="server" CssClass="radio" GroupName="checkpoint" onclick="Checkpoint()"  />
                                                </ItemTemplate>
                                            </asp:TemplateField>
                                            <asp:TemplateField HeaderText="Not Applicable" HeaderStyle-Width="5%" >
                                                <ItemTemplate>
                                                    <asp:RadioButton ID="Not_Applicable" runat="server" CssClass="radio" GroupName="checkpoint" onclick="Checkpoint()"  />
                                                </ItemTemplate>
                                            </asp:TemplateField>
                                            <asp:TemplateField HeaderText="Remarks" HeaderStyle-Width="20%" >
                                                    <ItemTemplate>
                                                        <asp:TextBox ID="Remarks" runat="server" CssClass="form-control form-control-sm col-sm-11 input" TextMode="MultiLine" Wrap="true" Width="95%" Enabled="false"  />
                                                    <asp:CustomValidator ID="CustomValidator3" runat="server" ClientValidationFunction="ValidateWithZero" ValidationGroup="submit" ControlToValidate="Remarks" ValidateEmptyText="true"></asp:CustomValidator>
                                                    </ItemTemplate>
                                                </asp:TemplateField>

                                        </Columns>    
                                        <EditRowStyle BackColor="#999999" />
                                <FooterStyle BackColor="#5D7B9D" ForeColor="White" Font-Bold="True" />
                                <HeaderStyle BackColor="#5D7B9D" Font-Bold="True" ForeColor="White" />
                                <PagerSettings Mode="Numeric" />
                                <PagerStyle BackColor="#284775" ForeColor="White" HorizontalAlign="Center" />
                                <RowStyle BackColor="#F7F6F3" ForeColor="#333333" />
                                <SelectedRowStyle BackColor="#E2DED6" Font-Bold="False" ForeColor="#333333" />
                                <SortedAscendingCellStyle BackColor="#E9E7E2" />
                                <SortedAscendingHeaderStyle BackColor="#506C8C" />
                                <SortedDescendingCellStyle BackColor="#FFFDF8" />
                                <SortedDescendingHeaderStyle BackColor="#6F8DAE" />
                                    </cc1:detailscontainer>
                            </div>
                            



                   </fieldset>
<%-------Checkpoint end-------%>

                    <div class="row">
                              <div class="form-group col-md-4 mb-1">
                                    <label for="Attachment" class="m-0 mr-2 p-0 col-form-label-sm  font-weight-bold fs-6" >Attachments<span class="text-danger">*</span></label>
                                    <asp:FileUpload runat="server" ID="Attachment" CssClass="form-control-file form-control-sm" AllowMultiple="true" />
                               </div>
                                                    
                     </div>
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
     <hr />
                    <div class="row m-0 justify-content-center mt-2">
                        <asp:Button ID="btnSave" runat="server" Text="Save"  OnClick="btnSave_Click" ValidationGroup="submit" CssClass="btn btn-sm btn-info" />
                        <asp:Button ID="btnDelete" runat="server" Text="Delete" Width="60" onclick="btnDelete_Click" CssClass="btn btn-sm btn-danger ml-1" OnClientClick="if(confirm('Do you want to Delete the selected record'))return true;else return false; "/>  
                    </div>
</div>
                </div>
            </div>

       
<%--</ContentTemplate>
</asp:UpdatePanel>--%>
     <script type="text/javascript" src="../../../Scripts/DynamicDropdown.js" ></script>   
    <script src="https://js.arcgis.com/4.30/"></script>
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
    </script>
</asp:Content>
