An item with the same key has already been added.


<asp:Button ID="btnNew" runat="server"  Text="New" OnClick="btnNew_Click" CssClass="btn btn-primary btn-sm" />


 protected void btnNew_Click(object sender, EventArgs e)
 {
     PageRecordDataSet.Clear();
     PageRecordDataSet.EnforceConstraints = false;
     Approval_Record.NewRecord();
     Div_InputField.Visible = true;
     btnSave.Visible = true;
 }
