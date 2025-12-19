// Approving Authority data
DataSet dsAuth = bIobj.GetApprovingAuthority(); // मान लो ये method है
// Division Head data
DataSet dsHead = bIobj.GetDivisionHead(((TextBox)Insurance_Record.Rows[0].FindControl("Division")).Text);

// नया DataTable बनाओ
DataTable dtCombined = new DataTable();
dtCombined.Columns.Add("NamePno");
dtCombined.Columns.Add("Pno");

// Approving Authority rows add करो
foreach (DataRow row in dsAuth.Tables[0].Rows)
{
    dtCombined.Rows.Add(row["Approving_Authority"].ToString(), row["Pno"].ToString());
}

// Division Head rows add करो
foreach (DataRow row in dsHead.Tables[0].Rows)
{
    dtCombined.Rows.Add(row["Hod_NamePno"].ToString(), row["Division_Head_Pno"].ToString());
}

// अब dropdown bind करो
ddlAuthority.DataSource = dtCombined;
ddlAuthority.DataTextField = "NamePno";   // दिखाने वाला text
ddlAuthority.DataValueField = "Pno";      // backend value
ddlAuthority.DataBind();

// Optional: default option
ddlAuthority.Items.Insert(0, new ListItem("--Select--", ""));



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
