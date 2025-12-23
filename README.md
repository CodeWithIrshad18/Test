protected void gvTraining_RowDataBound(object sender, GridViewRowEventArgs e)
{
    if (e.Row.RowType == DataControlRowType.DataRow)
    {
        // DB se aane wali rating (0-4)
        int skillRating = Convert.ToInt32(DataBinder.Eval(e.Row.DataItem, "SkillRating"));

        RadioButton r0 = (RadioButton)e.Row.FindControl("Radio1");
        RadioButton r1 = (RadioButton)e.Row.FindControl("Radio2");
        RadioButton r2 = (RadioButton)e.Row.FindControl("Radio3");
        RadioButton r3 = (RadioButton)e.Row.FindControl("Radio4");
        RadioButton r4 = (RadioButton)e.Row.FindControl("Radio5");

        // Pehle sab false
        r0.Checked = r1.Checked = r2.Checked = r3.Checked = r4.Checked = false;

        // Jo DB me hai wahi select
        switch (skillRating)
        {
            case 0: r0.Checked = true; break;
            case 1: r1.Checked = true; break;
            case 2: r2.Checked = true; break;
            case 3: r3.Checked = true; break;
            case 4: r4.Checked = true; break;
        }
    }
}




<style>
    .gv-input {
        height: 28px;
        font-size: 13px;
        padding: 2px 6px;
    }

    .gv-radio label {
        margin-right: 6px;
        font-weight: 600;
        cursor: pointer;
    }

    .rating-0 { color: #dc3545; }
    .rating-1 { color: #343a40; }
    .rating-2 { color: #6f42c1; }
    .rating-3 { color: #0d6efd; }
    .rating-4 { color: #198754; }

    .gv-delete {
        color: #dc3545;
        font-weight: bold;
        text-decoration: none;
    }

    .gv-delete:hover {
        color: #a71d2a;
    }

    .gv-header {
        background: #343a40;
        color: #fff;
        text-align: center;
        font-size: 13px;
    }
</style>

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

<asp:GridView ... 
    CssClass="table table-bordered table-hover table-sm"
    HeaderStyle-CssClass="gv-header">


 
 
 
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
