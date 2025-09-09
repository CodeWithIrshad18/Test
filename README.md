ScriptManager.RegisterStartupScript(
    this,
    this.GetType(),
    "alertRedirect",
    "alert('Record Approved Successfully'); window.location='" + Request.RawUrl + "';",
    true
);






if (result)
            {

                Year.SelectedValue = "";
                ddlyear.SelectedValue = "";
                Grid.DataSource = null;
                Grid.DataBind();
                PageRecordDataSet.Tables.Add("Table1");
                PageRecordDataSet.Tables.Add("App_Bonus_Generation_Details_Excel");
                MyMsgBox.show(CLMS.Control.MyMsgBox.MessageType.Success, "Record saved successfully !");
                btnsave.Visible = false;
                
                Response.Redirect(Request.RawUrl, false);
                ScriptManager.RegisterStartupScript(this, this.GetType(), "alert", "alert('Record Approved Successfully');", true);
            }
            else
            {
                MyMsgBox.show(CLMS.Control.MyMsgBox.MessageType.Errors, "Error While Saving !");
            }


            <div class="row m-0 justify-content-center mt-2">
                            <asp:Button CssClass="btn btn-sm btn-success" ID="btnsave" runat="server" OnClick="btnsave_Click" Text="Save"></asp:Button>
                        </div>
