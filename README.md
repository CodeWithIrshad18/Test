<asp:LinkButton ID="Year" runat="server" CommandName="select" 
    CausesValidation="false"
    Style="color:blue;font-weight:700" 
    Text='<%# Bind("Year") %>'>
</asp:LinkButton>

<script type="text/javascript">
    Sys.WebForms.PageRequestManager.getInstance().add_endRequest(function () {
        // re-initialize CalendarExtenders after UpdatePanel refresh
        $find("CalendarExtender1")._onButtonClick();
    });
</script>

<asp:ScriptManager ID="ScriptManager1" runat="server" EnablePartialRendering="true" />




this is my calendar extender 

    <div class="form-group col-md-4 mb-2">
            
                <label for="PaymentDate" class="m-0 mr-2 p-0 col-form-label-sm col-sm-5 font-weight-bold fs-6 justify-content-start">Payment Date:</label>
                <asp:TextBox ID="PaymentDate" runat="server" CssClass="form-control form-control-sm col-sm-6" ></asp:TextBox>

                      <asp:ImageButton ID="ImageButton2" runat="server" Height="28px" ImageUrl="~/Calendar.jpg" Width="23px"/>
                        <ask:CalendarExtender ID="CalendarExtender1" runat="server" Format="dd/MM/yyyy" PopupButtonID="ImageButton2" PopupPosition="BottomRight" TargetControlID="PaymentDate"/>
                      <asp:CustomValidator ID="CustomValidator21" runat="server" ClientValidationFunction="Validate" ValidationGroup="save" ControlToValidate="PaymentDate" ValidateEmptyText="true"></asp:CustomValidator>
</div>       


and this is my year searching textbox 

<asp:TemplateField HeaderText="Year" SortExpression="Vendor Code">
    <ItemTemplate>
        <asp:LinkButton ID="Year" runat="server" CommandName="select" style="color:blue;font-weight:700" Text='<%# Bind("Year") %>'></asp:LinkButton>
    </ItemTemplate>
</asp:TemplateField>

my issue Is without searching my calendar is opening in one click but when I search from this dropdown calendar is opening in 2nd click why
