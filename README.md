 <cc1:formcointainer ID="Approval_Record" runat="server" AllowPaging="True" AutoGenerateColumns="False"
                                PageSize="1" ShowHeader="False" Width="100%" DataSource="<%# PageRecordDataSet %>"
                                DataMember="App_Approval_Master" DataKeyNames="ID" OnNewRowCreatingEvent="Approval_Record_NewRowCreatingEvent"
                                 BindingErrorMessage="xxxxx" GridLines="None">
                                <Columns>
                                <asp:TemplateField SortExpression="ID" Visible="False">
                                    <ItemTemplate>
                                    <asp:Label ID="ID" runat="server" ></asp:Label>
                                    </ItemTemplate>
                                </asp:TemplateField>
                                <asp:TemplateField>
                                <ItemTemplate>
                                <div class="form-inline row">

                                     <div class="form-group col-md-6 mb-2">
                                       <label for="lblNature_Of_Job" class="m-0 mr-2 p-0 col-form-label-sm col-sm-5  font-weight-bold fs-6 justify-content-start">Nature Of Job:
                                           <span style="color: #FF0000;">*</span></label>
                                             <div class="form-group col-md-6 mb-2" style="margin-left:-14px">
                                            <div>
                                          <asp:UpdatePanel ID="UpdatePanel2" runat="server">
                                            <ContentTemplate>
                                                <div class="SearchCheckBoxList" style="width:287%;">
                                                          <button class="btn btn-sm btn-default selectArea w-100" type="button"  onclick="togglefloatDiv(this);" 
                                                              id="btn-dwn1" style="border:0.5px solid #ced2d5;">
                                                      <span  class="filter-option float-left">No item Selected</span>
                                                            <span class="caret" ></span> </button>
                                                          <div class="floatDiv" runat="server"  style="border:1px solid #ced2d5;position:absolute;z-index:1000;
                                                            box-shadow:0 6px 12px rgb(0 0 0 / 18%);background-color:white;padding:5px;display:none;width:100%;">
                                                           <asp:TextBox runat="server" ID="TextBox1" CssClass="form-control form-control-sm col-sm-12" 
                                                               oninput="filterCheckBox(this)" AutoComplete="off" ViewStateMode="Disabled" placeholder="Enter Nature Of Job" 
                                                               Font-Size="Smaller"/>
                                                    <div  style="overflow:auto;max-height:210px;overflow-y:auto;overflow-x:hidden"; class="searchList p-0" >
                                                                  <asp:CheckBoxList ID="Nature_Of_Job" runat="server"  CssClass="form-control-sm radio" >
                                                       <asp:ListItem Text="Project Insurance(PRI)" Value="Project Insurance(PRI)" />
                                                       <asp:ListItem Text="Fixed Assets Insurance(FAI)" Value="Fixed Assets Insurance(FAI)" />
                                                       <asp:ListItem Text="Inventory Insurance(INI)" Value="Fixed Assets Insurance(INI)" />
                                                       <asp:ListItem Text="Money Insurance(MNI)" Value="Money Insurance(MNI)" />
                                                       <asp:ListItem Text="Public Liablity Insurance(PLI)" Value="Public Liablity Insurance(PLI)" />
                                                       <asp:ListItem Text="Vehicle Insurance(VHI)" Value="Vehicle Insurance(VHI)" />
                                                       <asp:ListItem Text="Group Mediclaim Insurance(GMI)" Value="Group Mediclaim Insurance(GMI)" />
                                                       <asp:ListItem Text="Workmen Compensation Insurance(WCI)" Value="Workmen Compensation Insurance(WCI)" />
                                                       <asp:ListItem Text="Others(OTI)" Value="Others(OTI)" />
                                             </asp:CheckBoxList>
                                                         <%--<asp:CheckBoxList ID="Approving_Authority" runat="server" DataMember="ApprovingAuth" DataSource="<%# PageDDLDataset %>"
                                                                      DataTextField="Authority"
                                             DataValueField="Authority"  CssClass="form-control-sm radio" >
                                             </asp:CheckBoxList>--%>
                                                    </div>
                                                              <asp:TextBox runat="server" ID="TextBox3"  Width="0%" style="display:none" />
                                            </div>
                                                      </div>
                                                </ContentTemplate>
                                               </asp:UpdatePanel>
                                             </div>
                                          </div>
                              </div>
                                    <div class="form-group col-md-6 mb-2">
                                       <label for="lblPno" class="m-0 mr-2 p-0 col-form-label-sm col-sm-5  font-weight-bold fs-6 justify-content-start">Pno:<span style="color: #FF0000;">*</span></label>
                                        <%--<asp:TextBox ID="Front_Line_Officers" runat="server" CssClass="form-control form-control-sm col-sm-6"  AutoComplete="off"></asp:TextBox>--%>
                                               <div class="form-group col-md-6 mb-2" style="margin-left:-14px">
                                            <div>
                                           <asp:UpdatePanel ID="UpdatePanel3" runat="server">
                                            <ContentTemplate>
                                                <div class="SearchCheckBoxList" style="width: 146%;">
                                                          <button class="btn btn-sm btn-default selectArea w-100" type="button"  onclick="togglefloatDiv(this);" id="btn-dwn" style="border:0.5px solid #ced2d5;">
                                                             <span  class="filter-option float-left">No item Selected</span>
                                                             <span class="caret" ></span>
                                                          </button>
                                                          <div class="floatDiv" runat="server"  style="border:1px solid #ced2d5;position:absolute;z-index:1000;width:400px;box-shadow:0 6px 12px rgb(0 0 0 / 18%);max-height:400px;height:450px;background-color:white;padding:5px;display:none">
                                                             <asp:TextBox runat="server" ID="TextBox2" CssClass="form-control form-control-sm " oninput="filterCheckBox(this)" AutoComplete="off" ViewStateMode="Disabled" placeholder="Enter Pno." Font-Size="Smaller" />
                                                             <d
