<asp:GridView ID="gvManpower" runat="server" AutoGenerateColumns="false"
 CssClass="table table-bordered">

    <Columns>

        <asp:TemplateField HeaderText="Service Category">
            <ItemTemplate>
                <asp:Label ID="lblService" runat="server"
                    Text='<%# Eval("ServiceCategory") %>' />
            </ItemTemplate>
        </asp:TemplateField>

        <asp:TemplateField HeaderText="Male">
            <ItemTemplate>
                <asp:TextBox ID="txtMale" runat="server" />
            </ItemTemplate>
        </asp:TemplateField>

        <asp:TemplateField HeaderText="Female">
            <ItemTemplate>
                <asp:TextBox ID="txtFemale" runat="server" />
            </ItemTemplate>
        </asp:TemplateField>

        <asp:TemplateField HeaderText="Supervisor">
            <ItemTemplate>
                <asp:TextBox ID="txtSupervisor" runat="server" />
            </ItemTemplate>
        </asp:TemplateField>

    </Columns>
</asp:GridView>



An error occurred during local report processing.
The definition of the report 'C:\Users\EWEPA7818A\Desktop\Onboarding\POS\POS\App\Input\ModuleReport.rdlc' is invalid.
An unexpected error occurred in Report Processing.
Could not load file or assembly 'Microsoft.SqlServer.Types, Version=14.0.0.0, Culture=neutral, PublicKeyToken=89845dcd8080cc91' or one of its dependencies. The system cannot find the file specified.
