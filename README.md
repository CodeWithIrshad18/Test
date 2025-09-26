<div class="row g-3">

  <div class="col-md-4">
    <div class="form-group row align-items-center">
      <label for="Total_Eligible_Earned_Basic_Da" class="col-sm-5 col-form-label font-weight-bold fs-6">Total Eligible Earned (Basic+Da):</label>
      <div class="col-sm-7">
        <asp:TextBox ID="Total_Eligible_Earned_Basic_Da" runat="server" CssClass="form-control form-control-sm" Enabled="false"></asp:TextBox>
      </div>
    </div>
  </div>

  <div class="col-md-4">
    <div class="form-group row align-items-center">
      <label for="TotalBonusPayableAmount" class="col-sm-5 col-form-label font-weight-bold fs-6">Total Bonus Payable:</label>
      <div class="col-sm-7">
        <asp:TextBox ID="TotalBonusPayableAmount" runat="server" CssClass="form-control form-control-sm" Enabled="false"></asp:TextBox>
        <asp:CustomValidator ID="CustomValidator7" runat="server" ClientValidationFunction="Validate" ValidationGroup="save" ControlToValidate="TotalBonusPayableAmount" ValidateEmptyText="true"></asp:CustomValidator>
      </div>
    </div>
  </div>

  <div class="col-md-4">
    <div class="form-group row align-items-center">
      <label for="Total_advance_bonus" class="col-sm-5 col-form-label font-weight-bold fs-6">Total sum of deduction:</label>
      <div class="col-sm-7">
        <asp:TextBox ID="Total_advance_bonus" runat="server" CssClass="form-control form-control-sm" onkeyup="cal_net_bonus()"></asp:TextBox>
      </div>
    </div>
  </div>

</div>

                                   
                                   
                                   
                                   
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



align same , it is not aligned same 
