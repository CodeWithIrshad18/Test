protected void gvManpower_RowCreated(object sender, GridViewRowEventArgs e)
{
    if (e.Row.RowType == DataControlRowType.Header)
    {
        GridView gv = (GridView)sender;

        // ===== Header Row 1 =====
        GridViewRow row1 = new GridViewRow(0, 0, DataControlRowType.Header, DataControlRowState.Normal);

        row1.Cells.Add(new TableCell
        {
            Text = "Services Category",
            RowSpan = 3,
            HorizontalAlign = HorizontalAlign.Center
        });

        row1.Cells.Add(new TableCell
        {
            Text = "Standard ManPower Requirement",
            ColumnSpan = 5,
            HorizontalAlign = HorizontalAlign.Center
        });

        gv.Controls[0].Controls.AddAt(0, row1);

        // ===== Header Row 2 =====
        GridViewRow row2 = new GridViewRow(1, 0, DataControlRowType.Header, DataControlRowState.Normal);

        row2.Cells.Add(new TableCell
        {
            Text = "Unskilled",
            ColumnSpan = 3,
            HorizontalAlign = HorizontalAlign.Center
        });

        row2.Cells.Add(new TableCell
        {
            Text = "Skilled",
            ColumnSpan = 2,
            HorizontalAlign = HorizontalAlign.Center
        });

        gv.Controls[0].Controls.AddAt(1, row2);

        // ===== Header Row 3 =====
        GridViewRow row3 = new GridViewRow(2, 0, DataControlRowType.Header, DataControlRowState.Normal);

        row3.Cells.Add(new TableCell { Text = "Male" });
        row3.Cells.Add(new TableCell { Text = "Female" });
        row3.Cells.Add(new TableCell { Text = "Bush Cutting" });
        row3.Cells.Add(new TableCell { Text = "Supervisor" });
        row3.Cells.Add(new TableCell { Text = "Driver" });

        gv.Controls[0].Controls.AddAt(2, row3);
    }
}





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
