                <fieldset class="" style="border:1px solid #bfbebe;padding:5px 20px 5px 20px;border-radius:6px">
                  <cc1:DetailsContainer ID="Plandetails" runat="server" AutoGenerateColumns="False"
                            Width="100%" DataMember="App_Plandetails" DataKeyNames="Id" DataSource="<%# PageRecordDataSet %>"
                            ShowHeaderWhenEmpty="True" PageSize="100" 
                             OnRowDataBound="Plandetails_RowDataBound"
                            BindingErrorMessage="">
                         
                            <Columns>
                            
                                <asp:TemplateField HeaderText="ID" SortExpression="ID" Visible="False">
                                    <ItemTemplate>
                                        <asp:Label ID="ID" runat="server"></asp:Label>
                                    </ItemTemplate>
                                </asp:TemplateField>
                                
                               <asp:TemplateField HeaderText="EmployeeID" SortExpression="EmployeeID" Visible="False">
                                    <ItemTemplate>
                                        <asp:Label ID="EmployeeID" runat="server"></asp:Label>
                                    </ItemTemplate>
                                </asp:TemplateField>

                                <asp:TemplateField HeaderText="P.No">
    <ItemTemplate>
        <asp:TextBox ID="Pno" runat="server"
            CssClass="form-control form-control-sm gv-input text-center"
            AutoPostBack="True"
            OnTextChanged="Pno_TextChanged" />
    </ItemTemplate>
</asp:TemplateField>

                                 <asp:TemplateField HeaderText="Name">
    <ItemTemplate>
        <asp:TextBox ID="Name" runat="server"
            CssClass="form-control form-control-sm gv-input" />
    </ItemTemplate>
</asp:TemplateField>

                                <asp:TemplateField HeaderText="Designation">
    <ItemTemplate>
        <asp:TextBox ID="Designation" runat="server"
            CssClass="form-control form-control-sm gv-input" />
    </ItemTemplate>
</asp:TemplateField>


                                <asp:TemplateField HeaderText="Department">
    <ItemTemplate>
        <asp:TextBox ID="DepartmentName" runat="server"
            CssClass="form-control form-control-sm gv-input" />
    </ItemTemplate>
</asp:TemplateField>


                                
                                
                                <asp:TemplateField HeaderText="Skill Rating">
    <ItemTemplate>
        <div class="gv-radio text-center">
            <asp:RadioButton ID="Radio1" runat="server" GroupName="TrainingRatting"
                CssClass="rating-0" Text="0" Enabled="false" />
            <asp:RadioButton ID="Radio2" runat="server" GroupName="TrainingRatting"
                CssClass="rating-1" Text="1" Enabled="false" />
            <asp:RadioButton ID="Radio3" runat="server" GroupName="TrainingRatting"
                CssClass="rating-2" Text="2" Enabled="false" />
            <asp:RadioButton ID="Radio4" runat="server" GroupName="TrainingRatting"
                CssClass="rating-3" Text="3" Enabled="false" />
            <asp:RadioButton ID="Radio5" runat="server" GroupName="TrainingRatting"
                CssClass="rating-4" Text="4" Enabled="false" />
        </div>
    </ItemTemplate>
</asp:TemplateField>



                                <asp:TemplateField HeaderText="HOD Rating">
    <ItemTemplate>
        <div class="gv-radio text-center">
            <asp:RadioButton ID="Radio11" runat="server" GroupName="HOD_Rating" CssClass="rating-0" Text="0" />
            <asp:RadioButton ID="Radio12" runat="server" GroupName="HOD_Rating" CssClass="rating-1" Text="1" />
            <asp:RadioButton ID="Radio13" runat="server" GroupName="HOD_Rating" CssClass="rating-2" Text="2" />
            <asp:RadioButton ID="Radio14" runat="server" GroupName="HOD_Rating" CssClass="rating-3" Text="3" />
            <asp:RadioButton ID="Radio15" runat="server" GroupName="HOD_Rating" CssClass="rating-4" Text="4" />
        </div>
    </ItemTemplate>
</asp:TemplateField>
                                  <asp:TemplateField HeaderText="Remarks">
                                    <ItemTemplate>
                                        <asp:TextBox ID="Remarks" runat="server" Width="515px"></asp:TextBox>
                                        
                                    </ItemTemplate>
                                </asp:TemplateField>


                                <asp:TemplateField HeaderText="CreatedBy" Visible="False">
                                    <ItemTemplate>
                                        <asp:TextBox ID="CreatedBy" runat="server" Width="45px" Visible = "false" ></asp:TextBox>
                                        
                                    </ItemTemplate>
                                </asp:TemplateField>

                                <asp:TemplateField HeaderText="TNI_TYPE" Visible="False">
                                    <ItemTemplate>
                                        <asp:Label ID="TNI_Type" runat="server" Width="45px" Visible = "false" ></asp:Label>
                                        
                                    </ItemTemplate>
                                </asp:TemplateField>
                                <asp:TemplateField HeaderText="Priority" Visible="False">
                                    <ItemTemplate>
                                        <asp:Label ID="Priority" runat="server" Width="45px" Visible = "false" ></asp:Label>
                                        
                                    </ItemTemplate>
                                </asp:TemplateField>
                                 <asp:TemplateField>
    <ItemTemplate>
        <asp:LinkButton ID="lnkButton" runat="server"
            CommandArgument="<%# Container.DataItemIndex %>"
            CommandName="DELETEROW"
            CssClass="gv-delete"
            ToolTip="Delete Record">
            <i class="fa fa-trash"></i>
        </asp:LinkButton>
    </ItemTemplate>
</asp:TemplateField>


                               
                            </Columns>
                            <HeaderStyle BackColor="#30A8CF" />
                        </cc1:DetailsContainer>  
  </fieldset>
