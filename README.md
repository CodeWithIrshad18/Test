 <table style="border:thin;width:100%;border-color:black;border-color:#c2bdbd;background-color:#E2DED6" cellspacing="0" border="2">
        <tr>
           
            <td style ="text-align:center;color:black" rowspan="3">Sevices Category</td>
            <td colspan="5" style ="text-align:center;color:black;font-weight:bold">Standard Manpower Requirement</td>
       </tr>
       <tr>
            <td colspan="3" style ="text-align:center;color:black">Unskilled</td>
            <td colspan="2"  style ="text-align:center;color:black">Skilled</td>
        </tr>
        <tr>
            <td style ="text-align:center;color:black">Male</td>
            <td style ="text-align:center;color:black">Female</td>
            <td style ="text-align:center;color:black">Bush Cutting</td>
            <td style ="text-align:center;color:black">Supervisor</td>
            <td style ="text-align:center;color:black">Driver</td>
        </tr>

        <tr>
            <td class="ColumnField">
<asp:TextBox ID="Stnd_Manpower" runat="server" CssClass="form-control" BorderStyle="None" Width="200px" required="true" Enabled="false" />
            </td>
            <td class="ColumnField">
<asp:TextBox ID="Unskilled_Male" runat="server" CssClass="form-control" Height="30px" required="true" BorderStyle="None" onchange="male_validate()" OnTextChanged="Unskilled_Male_TextChanged" AutoPostBack="true" />
            </td>
             <td class="ColumnField">
<asp:TextBox ID="Unskilled_Female" runat="server" CssClass="form-control" Height="30px" required="true" BorderStyle="None"  onchange="female_validate()" OnTextChanged="Unskilled_Female_TextChanged" AutoPostBack="true" />
            </td>
            <td class="ColumnField">
<asp:TextBox ID="Bush_Cutting" runat="server" CssClass="form-control" Height="30px" required="true" BorderStyle="None"  onchange="Bush_validate()" OnTextChanged="Bush_Cutting_TextChanged" AutoPostBack="true"/>
            </td>
            <td class="ColumnField">
<asp:TextBox ID="Supervisor" runat="server" CssClass="form-control" Height="30px" required="true" BorderStyle="None"  onchange="sup_validate()" OnTextChanged="Supervisor_TextChanged" AutoPostBack="true"/>
            </td>
            <td class="ColumnField">
<asp:TextBox ID="Driver" runat="server" CssClass="form-control" Height="30px" required="true" BorderStyle="None"  onchange="dri_validate()" OnTextChanged="Driver_TextChanged" AutoPostBack="true"/>
            </td>
        </tr>
    </table>
