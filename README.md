this is my full code 

                     <fieldset class="" style="border:1px solid #bfbebe;padding:5px 20px 5px 20px;border-radius:6px">
                           <legend style="width:auto;border:0;font-size:14px;margin:0px 6px 0px 6px;padding:0px 5px 0px 5px;color:#0000FF"><b>Get Bonus Generation Details</b></legend>
                     
                         <div class="form-inline row">
                                        <div class="form-group col-md-4 mb-1">
                                            <label for="year" class="m-0 mr-2 p-0 col-form-label-sm col-sm-3 font-weight-bold fs-4"> Year:</label>
                                            <asp:DropDownList ID="ddlyear" runat="server" CssClass="form-control form-control-sm col-sm-4" style="Width:130px;height:33px">
                                                <asp:ListItem Value=""></asp:ListItem>                                                 
                                                <asp:ListItem Value="2024-2025">2024-2025</asp:ListItem>
                                                <asp:ListItem Value="2025-2026">2025-2026</asp:ListItem>
                                                <asp:ListItem Value="2026-2027">2026-2027</asp:ListItem>
                                            </asp:DropDownList>
                                            
                                           </div>
                                      

                                       <div class="form-group col-md-2 ">
                                           
                                           <asp:Button runat="server" ID="btnsearch" CssClass="btn btn-sm btn-success" Text="Search" OnClick="btnsearch_Click"/>
                                           
                                       </div>
                             </div>

                   </fieldset>

                      <fieldset class="" style="border:1px solid #bfbebe;padding:5px 20px 5px 20px;border-radius:6px">
                   <legend style="width:auto;border:0;font-size:14px;margin:0px 6px 0px 6px;padding:0px 5px 0px 5px;color:#0000FF"><b>Bonus Complience List</b></legend>
                          
                     <div class="w-100 border" style="overflow:auto;height:300px">
                        
                                
   

   <asp:GridView ID="Grid" runat="server" Style="text-align: center; width: 3000px; height: 100px;" AutoGenerateColumns="False"
   AllowPaging="false" CellPadding="0" GridLines="Both"  OnRowDataBound="Grid_RowDataBound"
   ForeColor="#333333" ShowHeaderWhenEmpty="True" SortedAscendingCellStyle-BorderColor="Black" SortedAscendingCellStyle-BorderWidth="1px" SortedAscendingCellStyle-BorderStyle="Solid"
   PageSize="100" PagerSettings-Visible="false" PagerStyle-HorizontalAlign="Center" RowStyle-Height="30px"
   PagerStyle-Wrap="True" HeaderStyle-Font-Size="small" RowStyle-Font-Size="small" CssClass="freezeColumn"
   HeaderStyle-HorizontalAlign="Center" RowStyle-ForeColor="Black" BorderColor="Black" BorderStyle="Solid" BorderWidth="1px">
   <AlternatingRowStyle BackColor="White" ForeColor="#284775" />


   <Columns>
       <asp:TemplateField HeaderText="Sl.No." SortExpression="Sl_No"
           HeaderStyle-Width="50px" HeaderStyle-HorizontalAlign="Center" ItemStyle-HorizontalAlign="Center">
           <ItemTemplate><%# Container.DataItemIndex + 1 + "."%></ItemTemplate>
       </asp:TemplateField>

  

       <asp:TemplateField HeaderText="ID" Visible="false">
           <ItemTemplate><asp:Label ID="lblID" runat="server" Text='<%# Eval("ID") %>' /></ItemTemplate>
       </asp:TemplateField>

       <asp:TemplateField HeaderText="Year">
           <ItemTemplate><asp:Label ID="lblYear" runat="server" Text='<%# Eval("Year") %>' /></ItemTemplate>
       </asp:TemplateField>
                                    
       <asp:TemplateField HeaderText="Vendor Code">
           <ItemTemplate><asp:Label ID="lblVcode" runat="server" Text='<%# Eval("Vcode") %>' /></ItemTemplate>
       </asp:TemplateField>

       <asp:TemplateField HeaderText="Aadhar No.">
           <ItemTemplate><asp:Label ID="lblAadharNo" runat="server" Text='<%# Eval("AadharNo") %>' /></ItemTemplate>
       </asp:TemplateField>

       <asp:TemplateField HeaderText="WorkMan Sl.No.">
           <ItemTemplate><asp:Label ID="lblWorkManSlno" runat="server" Text='<%# Eval("WorkManSlno") %>' /></ItemTemplate>
       </asp:TemplateField>

       <asp:TemplateField HeaderText="Workorder No.">
           <ItemTemplate><asp:Label ID="lblWorkorderNo" runat="server" Text='<%# Eval("WorkorderNo") %>' /></ItemTemplate>
       </asp:TemplateField>

       <asp:TemplateField HeaderText="WorkMan Category">
           <ItemTemplate><asp:Label ID="lblWorkManCategory" runat="server" Text='<%# Eval("WorkManCategory") %>' /></ItemTemplate>
       </asp:TemplateField>

       <asp:TemplateField HeaderText="WorkMan Name">
           <ItemTemplate><asp:Label ID="lblWorkManName" runat="server" Text='<%# Eval("WorkManName") %>' /></ItemTemplate>
       </asp:TemplateField>

       <asp:TemplateField HeaderText="No. of days worked">
           <ItemTemplate><asp:Label ID="lblTotaldaysWorked" runat="server" Text='<%# Eval("TotaldaysWorked") %>' /></ItemTemplate>
       </asp:TemplateField>

       <asp:TemplateField HeaderText="Total Earned (Basic+DA)">   <%--Total Wages 26 sep--%>
           <ItemTemplate><asp:Label ID="lblTotalWages" runat="server" Text='<%# Eval("TotalWages") %>' /></ItemTemplate>
       </asp:TemplateField>

       <asp:TemplateField HeaderText="Puja Bonus of other quota many Bonus Paid during the accounting year">
           <ItemTemplate><asp:Label ID="lblPuja_Bonus" runat="server" Text='<%# Eval("Puja_Bonus") %>' /></ItemTemplate>
       </asp:TemplateField>
                                      
       <asp:TemplateField HeaderText="Interim Bonus or Bonus paid in advance">
           <ItemTemplate><asp:Label ID="lblInterim_Bonus" runat="server" Text='<%# Eval("Interim_Bonus") %>' /></ItemTemplate>
       </asp:TemplateField>

       <asp:TemplateField HeaderText="Deduction on a/c of financial if any caused by misconduct of the employee">
           <ItemTemplate><asp:Label ID="lblDeduction_misconduct_emp" runat="server" Text='<%# Eval("Deduction_misconduct_emp") %>' /></ItemTemplate>
       </asp:TemplateField>

       <asp:TemplateField HeaderText="Total sum of Deduction">
           <ItemTemplate><asp:Label ID="lblTotal_deduction" runat="server" Text='<%# Eval("Total_deduction") %>' /></ItemTemplate>
       </asp:TemplateField>
       <asp:TemplateField HeaderText="Additional Amount"> <%--26 sep--%>
           <ItemTemplate><asp:Label ID="Additional_Amount" runat="server" Text='<%# Eval("Additional_Amount") %>' /></ItemTemplate>
       </asp:TemplateField>

     

       <asp:TemplateField HeaderText="Net Bonus Payable Amount">
           <ItemTemplate><asp:Label ID="lblBonusPayableAmount" runat="server" Text='<%# Eval("BonusPayableAmount") %>' /></ItemTemplate>
       </asp:TemplateField>

     
      <asp:TemplateField HeaderText="Bank Statement Sl.No.">
           <ItemTemplate>
               <asp:TextBox ID="BankStatementSlno" Text='<%# Eval("BankStatementSlno") %>' runat="server"></asp:TextBox>
           </ItemTemplate>
       </asp:TemplateField>



  </Columns>


  <EditRowStyle BackColor="#999999" />
  <FooterStyle BackColor="#5D7B9D" ForeColor="White" Font-Bold="True" />
  <HeaderStyle BackColor="#5D7B9D" Font-Bold="True" ForeColor="White" />
  <PagerSettings Mode="Numeric" />
  <PagerStyle BackColor="#284775" ForeColor="White" HorizontalAlign="Center" Font-Bold="True" CssClass="pager1" />
  <PagerStyle BackColor="#284775" ForeColor="White" HorizontalAlign="Center" />
  <RowStyle BackColor="#F7F6F3" ForeColor="#333333" />
  <SelectedRowStyle BackColor="#E2DED6" Font-Bold="False" ForeColor="#333333" />
  <SortedAscendingCellStyle BackColor="#E9E7E2" />
  <SortedAscendingHeaderStyle BackColor="#506C8C" />
  <SortedDescendingCellStyle BackColor="#FFFDF8" />
  <SortedDescendingHeaderStyle BackColor="#6F8DAE" />


</asp:GridView>







                     </div>
                          <div class="row m-0 mt-1" style="float:right">
                          <asp:Button ID="btnDwnld" runat="server" Text="Download All"   OnClick="btnDwnld_Click"  CssClass="btn btn-sm btn-info"   Style="width: 120px;border-radius: 31px;background-color:darkgreen;height: 27px;padding: 0px;   "/> &nbsp&nbsp&nbsp;
                           <asp:Button ID="btn_Draft_save" runat="server"  Text="Save as Draft"  CssClass="btn btn-sm btn" OnClick="btn_Draft_save_Click" BackColor="#0099ff" Font-Bold="true" ForeColor="White"  Width="260px"/>&emsp;&emsp;
                          </div>
                          
                           

                 </fieldset>

                    <fieldset  class="" style="border:1px solid #bfbebe;padding:5px 20px 5px 20px;border-radius:6px">
        <legend style="width:auto;border:0;font-size:14px;margin:0px 6px 0px 6px;padding:0px 5px 0px 5px;color:#0000FF"><b>Details</b></legend>
    <asp:UpdatePanel ID="UpdatePanel1" runat="server">
       <ContentTemplate>
        <cc1:formcointainer ID="Bonus_complience_Summary" runat="server" AllowPaging="True" AutoGenerateColumns="False" OnRowDataBound="Bonus_complience_Summary_RowDataBound"
                               PageSize="1" ShowHeader="False" Width="100%"  DataSource="<%# PageRecordDataSet %>"  
                               DataMember="App_Bonus_Comp_Summary" DataKeyNames="ID" OnNewRowCreatingEvent="Bonus_complience_Summary_NewRowCreatingEvent"           
                               BindingErrorMessage="aaaaaaaa" GridLines="None">
                     <Columns>
                          <asp:TemplateField SortExpression="ID" Visible="False">
                                       <ItemTemplate>
                                           <asp:Label ID="ID" runat="server"></asp:Label>
                                       </ItemTemplate>
                             </asp:TemplateField>

                           <asp:TemplateField>
                             <ItemTemplate>
                    <div class="form-inline row">    
                       <%-- <asp:ScriptManager ID="ScriptManager1" runat="server" ></asp:ScriptManager>--%>
                            <div class="form-group col-md-4 mb-2">
                                    
                                        <label for="PaymentDate" class="m-0 mr-2 p-0 col-form-label-sm col-sm-5 font-weight-bold fs-6 justify-content-start">Payment Date:</label>
                                        <asp:TextBox ID="PaymentDate" runat="server" CssClass="form-control form-control-sm col-sm-6" ></asp:TextBox>

                                              <asp:ImageButton ID="ImageButton2" runat="server" Height="28px" ImageUrl="~/Calendar.jpg" Width="23px"/>
                                                <ask:CalendarExtender ID="CalendarExtender1" runat="server" Format="dd/MM/yyyy" PopupButtonID="ImageButton2" PopupPosition="BottomRight" TargetControlID="PaymentDate"/>

                                              <asp:CustomValidator ID="CustomValidator21" runat="server" ClientValidationFunction="Validate" ValidationGroup="save" ControlToValidate="PaymentDate" ValidateEmptyText="true"></asp:CustomValidator>
                        </div>        
