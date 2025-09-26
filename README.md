                                    <div class="form-inline row">

                                         <div class="form-group col-md-4 mb-2">
                                        <label for="Total_net_bonus_payable" class="m-0 mr-2 p-0 col-form-label-sm col-sm-5  font-weight-bold fs-6 justify-content-start">Total Net Bonus Payable:</label>
                                          <asp:TextBox ID="Total_net_bonus_payable" runat="server" CssClass="form-control form-control-sm col-sm-6" Enabled="false"></asp:TextBox>
                                   <asp:CustomValidator ID="CustomValidator14" runat="server" ClientValidationFunction="Validate" ValidationGroup="save" ControlToValidate="Total_net_bonus_payable" ValidateEmptyText="true"></asp:CustomValidator>
                                    </div> 

                                         <div class="form-group col-md-4 mb-2">
                                        <label for="Total_net_bonus_paid" class="m-0 mr-2 p-0 col-form-label-sm col-sm-4  font-weight-bold fs-6 justify-content-start" >Total Net Bonus Paid Amount:</label>&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp
                                         <asp:TextBox ID="Total_net_bonus_paid" runat="server" CssClass="form-control form-control-sm col-sm-6" Enabled="false"></asp:TextBox>
                              </div>  

                                         <div class="form-group col-md-4 mb-2">
                                        <label for="Total_net_bonus_unpaid" class="m-0 mr-2 p-0 col-form-label-sm col-sm-4  font-weight-bold fs-6 justify-content-start">Total Net Bonus Unpaid Amount:</label>&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp
                                         <asp:TextBox ID="Total_net_bonus_unpaid" runat="server" CssClass="form-control form-control-sm col-sm-6" Enabled="false"></asp:TextBox>
                              </div>  



                                    </div>
