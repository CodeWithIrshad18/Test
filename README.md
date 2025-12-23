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

      <asp:TemplateField HeaderText="&nbsp;&nbsp;&nbsp;P.No." HeaderStyle-HorizontalAlign ="Left" HeaderStyle-Width="50px"  HeaderStyle-ForeColor="White">
         <ItemTemplate>
             <asp:TextBox ID="Pno" runat="server" Width="60px"   ForeColor="Black"  OnTextChanged="Pno_TextChanged" AutoPostBack="True"></asp:TextBox>
             
         </ItemTemplate>
          <HeaderStyle HorizontalAlign="Left" Width="50px" />
     </asp:TemplateField>

       <asp:TemplateField HeaderText="&nbsp;&nbsp;Name" HeaderStyle-HorizontalAlign ="Left" HeaderStyle-Width="140px" HeaderStyle-ForeColor="White">
         <ItemTemplate>
             <asp:TextBox ID="Name" runat="server" Width="130px"   ForeColor="Black"></asp:TextBox>
             
         </ItemTemplate>
           <HeaderStyle HorizontalAlign="Left" Width="140px" />
     </asp:TemplateField>
      <asp:TemplateField HeaderText="&nbsp;&nbsp;Designation" HeaderStyle-HorizontalAlign ="Left"  HeaderStyle-Width="140px" HeaderStyle-ForeColor="White">
         <ItemTemplate>
             <asp:TextBox ID="Designation" runat="server" Width="130px"   ForeColor="Black"></asp:TextBox>                                        
         </ItemTemplate>
          <HeaderStyle HorizontalAlign="Left" Width="140px" />
     </asp:TemplateField>

     <asp:TemplateField HeaderText="&nbsp;&nbsp;Department" HeaderStyle-HorizontalAlign ="Left"  HeaderStyle-Width="100px" HeaderStyle-ForeColor="White">
         <ItemTemplate>
             <asp:TextBox ID="DepartmentName" runat="server" Width="100px"   ForeColor="Black"></asp:TextBox>                                        
         </ItemTemplate>
          <HeaderStyle HorizontalAlign="Left" Width="100px" />
     </asp:TemplateField>

     
     
      <asp:TemplateField HeaderText="Skill Rating" HeaderStyle-Width="200px"  ItemStyle-HorizontalAlign="Center" HeaderStyle-HorizontalAlign="Center"  HeaderStyle-ForeColor="White">
         <ItemTemplate>
             <asp:RadioButton ID="Radio1" runat="server" GroupName="TrainingRatting" Text=" 0 " ForeColor="#FF3300" Font-Size="Medium" Font-Bold="True" Enabled="false" />
             <asp:RadioButton ID="Radio2" runat="server" GroupName="TrainingRatting" Text=" 1 " Font-Size="Medium" ForeColor="Black" Font-Bold="True"  Enabled="false" />
             <asp:RadioButton ID="Radio3" runat="server" GroupName="TrainingRatting" Text=" 2 " ForeColor="#CC66FF" Font-Size="Medium" Font-Bold="True"  Enabled="false" />
             <asp:RadioButton ID="Radio4" runat="server" GroupName="TrainingRatting" Text=" 3 " Font-Size="Medium" ForeColor="Blue" Font-Bold="True"  Enabled="false"  />
             <asp:RadioButton ID="Radio5" runat="server" GroupName="TrainingRatting" Text=" 4 " ForeColor="#006600" Font-Size="Medium" Font-Bold="True"  Enabled="false"  />
         </ItemTemplate>
          <HeaderStyle HorizontalAlign="Center" Width="200px" />
          <ItemStyle HorizontalAlign="Center" />
     </asp:TemplateField>

     <asp:TemplateField HeaderText="HOD Rating" HeaderStyle-Width="200px"  ItemStyle-HorizontalAlign="Center" HeaderStyle-HorizontalAlign="Center"  HeaderStyle-ForeColor="White">
         <ItemTemplate>
             <asp:RadioButton ID="Radio11" runat="server" GroupName="HOD_Rating" Text=" 0 " ForeColor="#FF3300" Font-Size="Medium" Font-Bold="True"/>
             <asp:RadioButton ID="Radio12" runat="server" GroupName="HOD_Rating" Text=" 1 " Font-Size="Medium" ForeColor="Black" Font-Bold="True"/>
             <asp:RadioButton ID="Radio13" runat="server" GroupName="HOD_Rating" Text=" 2 " ForeColor="#CC66FF" Font-Size="Medium" Font-Bold="True"/>
             <asp:RadioButton ID="Radio14" runat="server" GroupName="HOD_Rating" Text=" 3 " Font-Size="Medium" ForeColor="Blue" Font-Bold="True"/>
             <asp:RadioButton ID="Radio15" runat="server" GroupName="HOD_Rating" Text=" 4 " ForeColor="#006600" Font-Size="Medium" Font-Bold="True"/>
         </ItemTemplate>
          <HeaderStyle HorizontalAlign="Center" Width="200px" />
          <ItemStyle HorizontalAlign="Center" />
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
             <asp:LinkButton ID="lnkButton" runat="server" CommandArgument="<%# Container.DataItemIndex %>"
                 CommandName="DELETEROW"  Text=" X " ToolTip="Delete Record" CausesValidation="false"></asp:LinkButton>
         </ItemTemplate>
     </asp:TemplateField>

    
 </Columns>
