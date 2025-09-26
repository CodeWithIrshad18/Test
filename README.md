this is my full code of that 

       protected void btnsearch_Click(object sender, EventArgs e)
       {
           ScriptManager.RegisterStartupScript(this, GetType(), "txt_disable", "txt_disable();", true);
           string year = ddlyear.SelectedValue;
           string vcode = Session["UserName"].ToString();
           BL_Bonus_Complaince blobj = new BL_Bonus_Complaince();
           
           DataSet ds1 = new DataSet();
           
           ds1 = blobj.getdata(year, vcode);
           if (ds1.Tables[0].Rows.Count > 0)
           {
               string status = ds1.Tables[0].Rows[0]["Status"].ToString();
               if (status == "Returned to Vendor")
               {
                   get_bonus_data(year, vcode);
               }
               else

               {
                   MyMsgBox.show(CLMS.Control.MyMsgBox.MessageType.Errors, "Bonus Compliance entry of year " + year + " are already " + status + " .");
                   btnSave.Visible = false;
               }
               
           }
           else
           {

               get_bonus_data(year, vcode);

               
           }


                }


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

    <div class="form-group col-md-4 mb-2">
            
                <label for="PaymentDate" class="m-0 mr-2 p-0 col-form-label-sm col-sm-5 font-weight-bold fs-6 justify-content-start">Payment Date:</label>
                <asp:TextBox ID="PaymentDate" runat="server" CssClass="form-control form-control-sm col-sm-6" ></asp:TextBox>

                      <asp:ImageButton ID="ImageButton2" runat="server" Height="28px" ImageUrl="~/Calendar.jpg" Width="23px"/>
                        <ask:CalendarExtender ID="CalendarExtender1" runat="server" Format="dd/MM/yyyy" PopupButtonID="ImageButton2" PopupPosition="BottomRight" TargetControlID="PaymentDate"/>

                      <asp:CustomValidator ID="CustomValidator21" runat="server" ClientValidationFunction="Validate" ValidationGroup="save" ControlToValidate="PaymentDate" ValidateEmptyText="true"></asp:CustomValidator>
</div>       


still the issue is happening 
