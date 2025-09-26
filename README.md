<%@ Register Assembly="AjaxControlToolkit" Namespace="AjaxControlToolkit" TagPrefix="asp" %>

<!DOCTYPE html>
<html>
<head runat="server">
    <title>Calendar Extender Fix</title>
</head>
<body>
    <form id="form1" runat="server">

        <!-- ScriptManager is mandatory -->
        <asp:ScriptManager ID="ScriptManager1" runat="server" EnablePartialRendering="true" />

        <div class="container">

            <!-- Year Dropdown inside UpdatePanel -->
            <asp:UpdatePanel ID="UpdatePanel1" runat="server">
                <ContentTemplate>
                    <div class="form-group col-md-4 mb-1">
                        <label for="year" class="m-0 mr-2 p-0 col-form-label-sm col-sm-3 font-weight-bold fs-4"> Year:</label>
                        <asp:DropDownList ID="ddlyear" runat="server" 
                            CssClass="form-control form-control-sm col-sm-4" 
                            AutoPostBack="true" 
                            OnSelectedIndexChanged="ddlyear_SelectedIndexChanged"
                            style="Width:130px;height:33px">
                            <asp:ListItem Value=""></asp:ListItem>
                            <asp:ListItem Value="2024-2025">2024-2025</asp:ListItem>
                            <asp:ListItem Value="2025-2026">2025-2026</asp:ListItem>
                            <asp:ListItem Value="2026-2027">2026-2027</asp:ListItem>
                        </asp:DropDownList>
                    </div>
                </ContentTemplate>
            </asp:UpdatePanel>


            <!-- Calendar Extender -->
            <div class="form-group col-md-4 mb-2">
                <label for="PaymentDate" class="m-0 mr-2 p-0 col-form-label-sm col-sm-5 font-weight-bold fs-6 justify-content-start">Payment Date:</label>
                <asp:TextBox ID="PaymentDate" runat="server" CssClass="form-control form-control-sm col-sm-6"></asp:TextBox>

                <asp:ImageButton ID="ImageButton2" runat="server" Height="28px" ImageUrl="~/Calendar.jpg" Width="23px" />

                <asp:CalendarExtender ID="CalendarExtender1" runat="server"
                    Format="dd/MM/yyyy"
                    PopupButtonID="ImageButton2"
                    PopupPosition="BottomRight"
                    TargetControlID="PaymentDate" />

                <asp:CustomValidator ID="CustomValidator21" runat="server"
                    ClientValidationFunction="Validate"
                    ValidationGroup="save"
                    ControlToValidate="PaymentDate"
                    ValidateEmptyText="true"></asp:CustomValidator>
            </div>

        </div>

        <!-- Script to fix calendar rebind after async postback -->
        <script type="text/javascript">
            Sys.WebForms.PageRequestManager.getInstance().add_endRequest(function () {
                var cal = $find("CalendarExtender1");
                if (cal) {
                    cal._onButtonClick(); // ensures first click works
                }
            });
        </script>

    </form>
</body>
</html>




this is my searching for year 

 <div class="form-group col-md-4 mb-1">
     <label for="year" class="m-0 mr-2 p-0 col-form-label-sm col-sm-3 font-weight-bold fs-4"> Year:</label>
     <asp:DropDownList ID="ddlyear" runat="server" CssClass="form-control form-control-sm col-sm-4" style="Width:130px;height:33px">
         <asp:ListItem Value=""></asp:ListItem>                                                 
         <asp:ListItem Value="2024-2025">2024-2025</asp:ListItem>
         <asp:ListItem Value="2025-2026">2025-2026</asp:ListItem>
         <asp:ListItem Value="2026-2027">2026-2027</asp:ListItem>
     </asp:DropDownList>
     
    </div>

and this is my calendar extender 

    <div class="form-group col-md-4 mb-2">
            
                <label for="PaymentDate" class="m-0 mr-2 p-0 col-form-label-sm col-sm-5 font-weight-bold fs-6 justify-content-start">Payment Date:</label>
                <asp:TextBox ID="PaymentDate" runat="server" CssClass="form-control form-control-sm col-sm-6" ></asp:TextBox>

                      <asp:ImageButton ID="ImageButton2" runat="server" Height="28px" ImageUrl="~/Calendar.jpg" Width="23px"/>
                        <ask:CalendarExtender ID="CalendarExtender1" runat="server" Format="dd/MM/yyyy" PopupButtonID="ImageButton2" PopupPosition="BottomRight" TargetControlID="PaymentDate"/>
                      <asp:CustomValidator ID="CustomValidator21" runat="server" ClientValidationFunction="Validate" ValidationGroup="save" ControlToValidate="PaymentDate" ValidateEmptyText="true"></asp:CustomValidator>
</div>      


that issue is happening    
