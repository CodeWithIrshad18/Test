 <asp:TemplateField HeaderText="HOD Rating" HeaderStyle-Width="200px"  ItemStyle-HorizontalAlign="Center" HeaderStyle-HorizontalAlign="Center"  HeaderStyle-ForeColor="black">
                                    <ItemTemplate>
                                        <asp:RadioButton ID="Radio11" runat="server" GroupName='<%# "HOD_Rating" + Container.DataItemIndex %>' Text=" 0 " ForeColor="#FF3300" Font-Size="Medium" Font-Bold="true"/>&nbsp&nbsp&nbsp
                                        <asp:RadioButton ID="Radio12" runat="server" GroupName='<%# "HOD_Rating" + Container.DataItemIndex %>' Text=" 1 " ForeColor="Black" Font-Size="Medium" Font-Bold="true"/>&nbsp&nbsp&nbsp
                                        <asp:RadioButton ID="Radio13" runat="server" GroupName='<%# "HOD_Rating" + Container.DataItemIndex %>' Text=" 2 " ForeColor="#CC66FF" Font-Size="Medium" Font-Bold="true"/>&nbsp&nbsp&nbsp
                                        <asp:RadioButton ID="Radio14" runat="server" GroupName='<%# "HOD_Rating" + Container.DataItemIndex %>' Text=" 3 " ForeColor="Blue" Font-Size="Medium" Font-Bold="true"/>&nbsp&nbsp&nbsp
                                        <asp:RadioButton ID="Radio15" runat="server" GroupName='<%# "HOD_Rating" + Container.DataItemIndex %>' Text=" 4 " ForeColor="#006600" Font-Size="Medium" Font-Bold="true" />
                                           <asp:CustomValidator ID="CusV_Hod" runat="server" ClientValidationFunction="ValidateHodRatings" ErrorMessage="*" Font-Size="Medium" Display="Dynamic" ForeColor="Red" ValidationGroup="save"></asp:CustomValidator>

                            </ItemTemplate>
                                     <HeaderStyle HorizontalAlign="Center" Width="200px" />
                                     <ItemStyle HorizontalAlign="Center" />
                                </asp:TemplateField>



<script type="text/javascript">


    function ValidateHodRatings(src, args) {

        var table = document.getElementById("<%= Plandetails.ClientID %>");
     var rows = table.getElementsByTagName("tr");

    
     for (var i = 1; i < rows.length; i++) {
         
         var radios = rows[i].querySelectorAll('input[type=radio][name*="HOD_Rating"]');

         if (radios.length === 0) continue; 

         var checked = false;

         for (var j = 0; j < radios.length; j++) {
             if (radios[j].checked) {
                 checked = true;
                 break;
             }
         }

        
         if (!checked) {
             alert("HOD Rating Selection is mandatory for all rows.")
             args.IsValid = false;
             return;
         }
     }

     
     args.IsValid = true;
 }

</script>
