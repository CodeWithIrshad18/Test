public DataSet GetRecordsFOrResult(string userId)
{
    string strSQL = @"
        SELECT 
            amm.ID,
            amm.ModuleName,
            CASE 
                WHEN NOT EXISTS (
                    SELECT 1
                    FROM App_QuestionMaster q
                    WHERE q.ModuleID = amm.ID
                      AND q.IsActive = 1
                      AND NOT EXISTS (
                            SELECT 1
                            FROM ASP_User_Response r
                            WHERE r.QuestionID = q.Id
                              AND r.UserID = @UserId
                      )
                )
                THEN 'Done'
                ELSE 'Not Done'
            END AS module_status
        FROM App_Module_Master amm
        ORDER BY amm.SerialNo ASC";

    DataHelper dh = new DataHelper();
    Dictionary<string, object> objParam = new Dictionary<string, object>();
    objParam.Add("@UserId", userId);

    return dh.GetDataset(strSQL, "App_Quiz_Result", objParam);
}
protected void btnSearch_Click(object sender, EventArgs e)
{
    BL_ModuleResults bL_ModuleResults = new BL_ModuleResults();
    DataSet dataSetr = bL_ModuleResults.GetRecordsFOrResult(email.Text);
    PageRecordsDataSet.Merge(dataSetr, true, MissingSchemaAction.Ignore);
    Results.BindData();
}

protected void ApproverRecords_RowDataBound(object sender, GridViewRowEventArgs e)
{
    if (e.Row.RowType == DataControlRowType.DataRow)
    {
        DataRowView drv = (DataRowView)e.Row.DataItem;

        ((Label)e.Row.FindControl("ModuleName")).Text = drv["ModuleName"].ToString();
        ((Label)e.Row.FindControl("module_status")).Text = drv["module_status"].ToString();
    }
}


 
 
 
 
 public DataSet GetRecordsFOrResult(string userid)
 {
     
     string strSQL = "select amm.ID,amm.ModuleName,case when count(distinct aqm.Id) = COUNT(distinct  aur.QuestionID) then 'Done' else 'Not Done' end as module_status	from App_Module_Master amm 	left join APP_questionmaster aqm on amm.ID = aqm.ModuleID left join ASP_User_Response aur on aur.QuestionID = aqm.Id and aur.userid = " + userid;
     DataHelper dh = new DataHelper();
     Dictionary<string, object> objParam = null;
     
     strSQL += " group by amm.ID,amm.modulename,amm.SerialNo";
     strSQL += " order by amm.SerialNo asc";
     return dh.GetDataset(strSQL, "App_Quiz_Result", objParam);

 }


protected void btnSearch_Click(object sender, EventArgs e)
{
    BL_ModuleResults bL_ModuleResults = new BL_ModuleResults();
    
    //GetRecords(GetFilterCondition(), Results.PageSize, 10, "");
    DataSet dataSetr = new DataSet();
    dataSetr = bL_ModuleResults.GetRecordsFOrResult(email.Text);
    PageRecordsDataSet.Merge(dataSetr, true, MissingSchemaAction.Ignore);
    Results.BindData();
}

      <cc1:detailscontainer ID="Results" runat="server" AutoGenerateColumns="False"
            AllowPaging="False" CellPadding="4" GridLines="None" Width="100%" DataMember="App_Quiz_Result"
            DataKeyNames="ID" DataSource="<%# PageRecordsDataSet %>"
            ForeColor="#333333" ShowHeaderWhenEmpty="True" PageSize="10" PagerSettings-Visible="True" PagerStyle-HorizontalAlign="Center" 
      PagerStyle-Wrap="False" HeaderStyle-Font-Size="Smaller" RowStyle-Font-Size="Smaller" CssClass="table" OnRowDataBound="ApproverRecords_RowDataBound" >
            <AlternatingRowStyle BackColor="White" ForeColor="#284775" />
          
            <Columns>
                <asp:TemplateField >
                    <HeaderTemplate>
                    <span>Module Name</span>
                </HeaderTemplate>
                    <ItemTemplate>
                        <asp:Label ID="ModuleName" runat="server"></asp:Label>
                    </ItemTemplate>
                </asp:TemplateField>

                <asp:TemplateField >
                     <HeaderTemplate>
                    <span>Status</span>
                </HeaderTemplate>
                    <ItemTemplate>
                        <asp:Label ID="module_status" runat="server"></asp:Label>
                    </ItemTemplate>
                </asp:TemplateField>
                
                

               
         
        
            </Columns>    
              <EditRowStyle BackColor="#999999" />
<FooterStyle BackColor="#5D7B9D" ForeColor="White" Font-Bold="True" />
<HeaderStyle BackColor="#5D7B9D" Font-Bold="True" ForeColor="White" />
<PagerSettings Mode="Numeric" />
<PagerStyle BackColor="#284775" ForeColor="White" HorizontalAlign="Center" Font-Bold="True" CssClass="pager1" />
<PagerStyle BackColor="#284775" ForeColor="White" HorizontalAlign="Center" />
<RowStyle BackColor="#F7F6F3" ForeColor="#333333"/>
<SelectedRowStyle BackColor="#E2DED6" Font-Bold="False" ForeColor="#333333" />
<SortedAscendingCellStyle BackColor="#E9E7E2" />
<SortedAscendingHeaderStyle BackColor="#506C8C" />
<SortedDescendingCellStyle BackColor="#FFFDF8" />
<SortedDescendingHeaderStyle BackColor="#6F8DAE" />
        </cc1:detailscontainer>


SELECT COUNT(*) 
FROM App_QuestionMaster Q
WHERE Q.IsActive = 1
AND NOT EXISTS (
    SELECT 1 FROM ASP_User_Response R
    WHERE R.QuestionID = Q.Id
    AND R.UserID = '159445'
)
