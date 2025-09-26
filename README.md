<div class="form-inline row">

    <div class="form-group col-md-4 mb-2">
        <label for="Total_Eligible_Earned_Basic_Da" class="m-0 mr-2 p-0 col-form-label-sm col-sm-5 font-weight-bold fs-6 justify-content-start">
            Total Eligible Earned (Basic+Da):
        </label>
        <asp:TextBox ID="Total_Eligible_Earned_Basic_Da" runat="server" CssClass="form-control form-control-sm col-sm-6" Enabled="false"></asp:TextBox>
        <%--<asp:CustomValidator ID="CustomValidator3" runat="server" ClientValidationFunction="Validate" ValidationGroup="save" ControlToValidate="Total_Eligible_Earned_Basic_Da" ValidateEmptyText="true"></asp:CustomValidator>--%>
    </div>

    <div class="form-group col-md-4 mb-2">
        <label for="TotalBonusPayableAmount" class="m-0 mr-2 p-0 col-form-label-sm col-sm-5 font-weight-bold fs-6 justify-content-start">
            Total Bonus Payable:
        </label>
        <asp:TextBox ID="TotalBonusPayableAmount" runat="server" CssClass="form-control form-control-sm col-sm-6" Enabled="false"></asp:TextBox>
        <asp:CustomValidator ID="CustomValidator7" runat="server" ClientValidationFunction="Validate" ValidationGroup="save" ControlToValidate="TotalBonusPayableAmount" ValidateEmptyText="true"></asp:CustomValidator>
    </div>

    <div class="form-group col-md-4 mb-2">
        <label for="Total_advance_bonus" class="m-0 mr-2 p-0 col-form-label-sm col-sm-5 font-weight-bold fs-6 justify-content-start">
            Total sum of deduction:
        </label>
        <asp:TextBox ID="Total_advance_bonus" runat="server" CssClass="form-control form-control-sm col-sm-6" onkeyup="cal_net_bonus()"></asp:TextBox>
    </div>

</div>




I want same like this alignment 

 <div class="form-inline row"> 
    <div class="form-group col-md-4 mb-2">
              <label for="No_Worker_Paid" class="m-0 mr-2 p-0 col-form-label-sm col-sm-5  font-weight-bold fs-6 justify-content-start">No. of Paid Worker:</label>
                  <asp:TextBox ID="No_Worker_Paid" runat="server" CssClass="form-control form-control-sm col-sm-6" Enabled="false"></asp:TextBox>
        <%--<asp:CustomValidator ID="CustomValidator6" runat="server" ClientValidationFunction="Validate" ValidationGroup="save" ControlToValidate="No_Worker_Paid" ValidateEmptyText="true"></asp:CustomValidator>
        <asp:RegularExpressionValidator runat="server" ID="RegularExpressionValidator4"  ControlToValidate="No_Worker_Paid"  ValidationExpression="^[0-9]+$" ErrorMessage="Invalid" ValidationGroup="save" Display="Dynamic" ForeColor="Red" Font-Size="XX-Small" />--%>
          </div>  


     <div class="form-group col-md-4 mb-2">
              <label for="Unpaid_workers" class="m-0 mr-2 p-0 col-form-label-sm col-sm-5  font-weight-bold fs-6 justify-content-start">No. of UnPaid Worker:</label>
                  <asp:TextBox ID="Unpaid_workers" runat="server" CssClass="form-control form-control-sm col-sm-6" Enabled="false"></asp:TextBox>
        <%--<asp:CustomValidator ID="CustomValidator11" runat="server" ClientValidationFunction="Validate" ValidationGroup="save" ControlToValidate="Unpaid_workers" ValidateEmptyText="true"></asp:CustomValidator>
        <asp:RegularExpressionValidator runat="server" ID="RegularExpressionValidator5"  ControlToValidate="Unpaid_workers"  ValidationExpression="^[0-9]+$" ErrorMessage="Invalid" ValidationGroup="save" Display="Dynamic" ForeColor="Red" Font-Size="XX-Small" />--%>
          </div> 
     

     <div class="form-group col-md-4 mb-2">
              <label for="Total_gross_wage" class="m-0 mr-2 p-0 col-form-label-sm col-sm-5  font-weight-bold fs-6 justify-content-start">Total Earned(Basic+Da):</label><%-- 26 sep--%>
                  <asp:TextBox ID="Total_gross_wage" runat="server" CssClass="form-control form-control-sm col-sm-6" Enabled="false"></asp:TextBox>
       <%-- <asp:CustomValidator ID="CustomValidator12" runat="server" ClientValidationFunction="Validate" ValidationGroup="save" ControlToValidate="Total_gross_wage" ValidateEmptyText="true"></asp:CustomValidator>
        <asp:RegularExpressionValidator runat="server" ID="RegularExpressionValidator7"  ControlToValidate="Total_gross_wage"  ValidationExpression="^[0-9]+$" ErrorMessage="Invalid" ValidationGroup="save" Display="Dynamic" ForeColor="Red" Font-Size="XX-Small" />--%>
          </div>
  
 </div>

for this html 

                                   <div class="form-inline row">

                                                                              <div class="form-group col-md-4 mb-2">
                                        <label for="Total_Eligible_Earned_Basic_Da" class="m-0 mr-2 p-0 col-form-label-sm col-sm-5  font-weight-bold fs-6 justify-content-start">Total Eligible Earned (Basic+Da):</label>
                                          <asp:TextBox ID="Total_Eligible_Earned_Basic_Da" runat="server" CssClass="form-control form-control-sm col-sm-6" Enabled="false"></asp:TextBox>
<%--                                   <asp:CustomValidator ID="CustomValidator3" runat="server" ClientValidationFunction="Validate" ValidationGroup="save" ControlToValidate="Total_Eligible_Earned_Basic_Da" ValidateEmptyText="true"></asp:CustomValidator>--%>
                                    </div
                                                                                      <div class="form-group col-md-4 mb-2">
     <label for="TotalLeavePayabledays" class="m-0 mr-2 p-0 col-form-label-sm col-sm-5  font-weight-bold fs-6 justify-content-start">Total Bonus Payable:</label>
       <asp:TextBox ID="TotalBonusPayableAmount" runat="server" CssClass="form-control form-control-sm col-sm-6" Enabled="false"></asp:TextBox>
<asp:CustomValidator ID="CustomValidator7" runat="server" ClientValidationFunction="Validate" ValidationGroup="save" ControlToValidate="TotalBonusPayableAmount" ValidateEmptyText="true"></asp:CustomValidator>
 </div> 

                                         <div class="form-group col-md-4 mb-2">
    <label for="Total_advance_bonus" class="m-0 mr-2 p-0 col-form-label-sm col-sm-5  font-weight-bold fs-6 justify-content-start">Total sum of deduction:</label>
      <asp:TextBox ID="Total_advance_bonus" runat="server" CssClass="form-control form-control-sm col-sm-6"  onkeyup="cal_net_bonus()" ></asp:TextBox>
</div> 

                                       </div>
