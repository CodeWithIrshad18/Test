<td width="170px">
  <asp:DropDownList ID="EXECHEAD" runat="server" CssClass="ColumnFieldControls" DataMember="EMA_EXE_HEAD"
                                                        DataSource="<%# PageDDLDataset %>" DataTextField="EMA_EXE_HEAD" DataValueField="EMA_EXE_HEAD"
                                                        Height="22px"  Width ="150px" OnSelectedIndexChanged="EXECHEAD_SelectedIndexChanged" AutoPostBack="true">
                                                    </asp:DropDownList>
 <asp:RequiredFieldValidator ValidationGroup="View" ID="RequiredFieldValidator2" runat="server"  ErrorMessage="*" ControlToValidate="EXECHEAD" ForeColor="Red" Font-Size="Large" Font-Bold="True"></asp:RequiredFieldValidator>
</td>
<td>
Department :
</td>
<td width="220px">
   <asp:DropDownList ID="DepartmentID" runat="server" 
        CssClass="ColumnFieldControls" DataMember="EMA_DEPT_DESC"
                                                        
        DataSource="<%# PageDDLDataset %>" DataTextField="EMA_DEPT_DESC" DataValueField="EMA_DEPT_DESC"
                                                        Height="22px"  
        Width ="150px"  OnSelectedIndexChanged="DepartmentID_SelectedIndexChanged" 
        AutoPostBack="true">
   </asp:DropDownList>
</td>
 <td  width="50px">
Section :
</td>
<td>
   <asp:DropDownList ID="SectionID" runat="server" CssClass="ColumnFieldControls" DataMember="EMA_SECTION"
                                                        
        DataSource="<%# PageDDLDataset %>" DataTextField="EMA_SECTION" DataValueField="EMA_SECTION"
                                                        Height="22px"  
        Width ="100px" OnSelectedIndexChanged="SectionID_SelectedIndexChanged1"
        AutoPostBack="false">
                                                    </asp:DropDownList>
</td>


 protected void EXECHEAD_SelectedIndexChanged(object sender, EventArgs e)
 {
     BL_ShiftMaster blobj = new BL_ShiftMaster();
     PageDDLDataset.Tables["EMA_DEPT_DESC"].Clear();
     PageDDLDataset.Tables["EMA_SECTION"].Clear();
     //PageDDLDataset.Merge(blobj.GetDataDept("select '' as EMA_DEPT_DESC,0 as sl from EMPL_RFID union  select distinct EMA_DEPT_DESC,1 as sl from EMPL_RFID where  EMA_EXE_HEAD='" + EXECHEAD.SelectedValue + "' order by sl,EMA_DEPT_DESC"));
     //DepartmentID.DataBind();
     //PageDDLDataset.Merge(blobj.GetDataSection("select '' as EMA_SECTION,0 as sl from EMPL_RFID union  select distinct EMA_SECTION,1 as sl from EMPL_RFID where  EMA_DEPT_DESC='" + DepartmentID.SelectedValue + "' order by sl,EMA_SECTION"));
     //SectionID.DataBind();
     //PageDDLDataset = blobj.GetDataDept("select '' as EMA_DEPT_DESC,0 as sl from SAPHRDB.dbo.T_EMPL_ALL where EMA_COMP_CODE = '3000' and EMA_DISCH_DT is null  and EMA_PYRL_AREA IN ('ZZ', 'JS')   union  select distinct EMA_DEPT_DESC,1 as sl from SAPHRDB.dbo.T_EMPL_ALL where EMA_COMP_CODE = '3000' and EMA_DISCH_DT is null  and EMA_PYRL_AREA IN ('ZZ', 'JS')  order by sl,EMA_DEPT_DESC --  and  EMA_EXEC_HEAD_DESC=''");

     PageDDLDataset = blobj.GetDataDept("select '' as EMA_DEPT_DESC,0 as sl from SAPHRDB.dbo.T_EMPL_ALL where EMA_COMP_CODE = '3000' and EMA_DISCH_DT is null  and EMA_PYRL_AREA IN ('ZZ', 'JS')   union  select distinct EMA_DEPT_DESC,1 as sl from SAPHRDB.dbo.T_EMPL_ALL where EMA_COMP_CODE = '3000' and EMA_DISCH_DT is null  and EMA_PYRL_AREA IN ('ZZ', 'JS')  and  EMA_EXEC_HEAD_DESC='" + EXECHEAD.SelectedValue + "' order by sl,EMA_DEPT_DESC");
     DepartmentID.DataBind();
     // as sl from SAPHRDB.dbo.T_EMPL_ALL where EMA_COMP_CODE = '3000' and EMA_DISCH_DT is null and EMA_PYRL_AREA IN ('ZZ', 'JS')   order by sl,EMA_SECTION--and EMA_DEPT_DESC = ''
     //PageDDLDataset.Merge(blobj.GetDataSection("select '' as EMA_SECTION,0 as sl from SAPHRDB.dbo.T_EMPL_ALL where EMA_COMP_CODE = '3000' and EMA_DISCH_DT is null  and EMA_PYRL_AREA IN ('ZZ', 'JS')    union  select distinct EMA_SECTION_DESC as EMA_SECTION,1 as sl from SAPHRDB.dbo.T_EMPL_ALL where EMA_COMP_CODE = '3000' and EMA_DISCH_DT is null  and EMA_PYRL_AREA IN ('ZZ', 'JS')  and  EMA_DEPT_DESC='" + DepartmentID.SelectedValue + "' order by sl,EMA_SECTION_DESC"));
     PageDDLDataset.Merge(blobj.GetDataSection("select '' as EMA_SECTION,0 as sl from SAPHRDB.dbo.T_EMPL_ALL where EMA_COMP_CODE = '3000' and EMA_DISCH_DT is null  and EMA_PYRL_AREA IN ('ZZ', 'JS')    union  select distinct EMA_SECTION_DESC as EMA_SECTION,1 as sl from SAPHRDB.dbo.T_EMPL_ALL where EMA_COMP_CODE = '3000' and EMA_DISCH_DT is null  and EMA_PYRL_AREA IN ('ZZ', 'JS')  and  EMA_EXEC_HEAD_DESC='" + EXECHEAD.SelectedValue + "' order by sl,EMA_SECTION"));
     SectionID.DataBind();
 }

 protected void DepartmentID_SelectedIndexChanged(object sender, EventArgs e)
 {
     BL_ShiftMaster blobj = new BL_ShiftMaster();
     PageDDLDataset.Tables["EMA_SECTION"].Clear();
     PageDDLDataset=blobj.GetDataSection("select '' as  as EMA_SECTION,0 as sl from SAPHRDB.dbo.T_EMPL_ALL where EMA_COMP_CODE = '3000' and EMA_DISCH_DT is null  and EMA_PYRL_AREA IN ('ZZ', 'JS')    union  select distinct EMA_SECTION_DESC as EMA_SECTION,1 as sl from SAPHRDB.dbo.T_EMPL_ALL where EMA_COMP_CODE = '3000' and EMA_DISCH_DT is null  and EMA_PYRL_AREA IN ('ZZ', 'JS')  and  EMA_DEPT_DESC='" + DepartmentID.SelectedValue + "' order by sl,EMA_SECTION_DESC");
     // PageDDLDataset.Merge(blobj.GetDataSection("select '' as  as EMA_SECTION,0 as sl from SAPHRDB.dbo.T_EMPL_ALL where EMA_COMP_CODE = '3000' and EMA_DISCH_DT is null  and EMA_PYRL_AREA IN ('ZZ', 'JS')    union  select distinct EMA_SECTION_DESC as EMA_SECTION,1 as sl from SAPHRDB.dbo.T_EMPL_ALL where EMA_COMP_CODE = '3000' and EMA_DISCH_DT is null  and EMA_PYRL_AREA IN ('ZZ', 'JS')  and  EMA_DEPT_DESC='" + DepartmentID.SelectedValue + "' order by sl,EMA_SECTION_DESC"));
     SectionID.DataBind();
     if (PageDDLDataset.Tables["EMA_PERNO"] != null)
         PageDDLDataset.Tables["EMA_PERNO"].Clear();
     //PageDDLDataset.Merge(blobj.GetDataPno("select '' as EMA_PERNO,'' as Description,0 as sl from SAPHRDB.dbo.T_EMPL_ALL where EMA_COMP_CODE = '3000' and EMA_DISCH_DT is null  and EMA_PYRL_AREA IN ('ZZ', 'JS')   union  select distinct EMA_PERNO,(EMA_PERNO + ' - '+EMA_ENAME) as Description,1 as sl from SAPHRDB.dbo.T_EMPL_ALL where   EMA_DEPT_DESC='" + DepartmentID.SelectedValue + "' and EMA_COMP_CODE = '3000' and EMA_DISCH_DT is null  and EMA_PYRL_AREA IN ('ZZ', 'JS')   order by sl,EMA_PERNO "));
     PageDDLDataset.Merge(blobj.GetDataPno("select '' as EMA_PERNO,'' as Description,0 as sl from SAPHRDB.dbo.T_EMPL_ALL where EMA_COMP_CODE = '3000' and EMA_DISCH_DT is null  and EMA_PYRL_AREA IN ('ZZ', 'JS')   union  select distinct EMA_PERNO,(EMA_PERNO + ' - '+EMA_ENAME) as Description,1 as sl from SAPHRDB.dbo.T_EMPL_ALL where  EMA_DEPT_DESC='" + DepartmentID.SelectedValue + "' and EMA_COMP_CODE = '3000' and EMA_DISCH_DT is null  and EMA_PYRL_AREA IN ('ZZ', 'JS')   order by sl,EMA_PERNO "));

     //PageDDLDataset.Merge(blobj.GetDataSection("select '' as EMA_SECTION,0 as sl from EMPL_RFID union  select distinct EMA_SECTION,1 as sl from EMPL_RFID where  EMA_DEPT_DESC='" + DepartmentID.SelectedValue + "'  and EMA_SECTION !=' ' order by sl,EMA_SECTION"));
     //PageDDLDataset.Tables["EMA_PERNO"].Clear();
     //PageDDLDataset.Merge(blobj.GetDataPno("select '' as EMA_PERNO,'' as Description,0 as sl from EMPL_RFID union  select distinct EMA_PERNO as EMA_PERNO,(EMA_PERNO || ' - '||EMA_ENAME) as Description,1 as sl from EMPL_RFID where  EMA_DEPT_DESC='" + DepartmentID.SelectedValue + "' order by sl,EMA_PERNO"));
     
     Pno.DataBind();
   
 }
 protected void SectionID_SelectedIndexChanged(object sender, EventArgs e)
 {
     BL_ShiftMaster blobj = new BL_ShiftMaster();
     PageDDLDataset.Tables["EMA_PERNO"].Clear();
     //if (SectionID.SelectedIndex > 0)
     //{
     //    PageDDLDataset.Merge(blobj.GetDataPno("select '' as EMA_PERNO,'' as Description,0 as sl from EMPL_RFID union  select distinct EMA_PERNO as EMA_PERNO,(EMA_PERNO || ' - '||EMA_ENAME) as Description,1 as sl from EMPL_RFID where EMA_SECTION='" + SectionID.SelectedValue + "' order by sl,EMA_PERNO"));
     //}
     //else
     //{
     //    PageDDLDataset.Merge(blobj.GetDataPno("select '' as EMA_PERNO,'' as Description,0 as sl from EMPL_RFID union  select distinct EMA_PERNO as EMA_PERNO,(EMA_PERNO || ' - '||EMA_ENAME) as Description,1 as sl from EMPL_RFID where  EMA_DEPT_DESC='" + DepartmentID.SelectedValue + "' order by sl,EMA_PERNO"));
     //}
     if (SectionID.SelectedIndex > 0)
     {
         PageDDLDataset.Merge(blobj.GetDataPno("select '' as EMA_PERNO,'' as Description,0 as sl from EMPL_RFID union  select distinct EMA_PERNO as EMA_PERNO,(EMA_PERNO + ' - '+EMA_ENAME) as Description,1 as sl from SAPHRDB.dbo.T_EMPL_ALL where EMA_SECTION_DESC='" + SectionID.SelectedValue + "' order by sl,EMA_PERNO"));
     }
     else
     {
         PageDDLDataset.Merge(blobj.GetDataPno("select '' as EMA_PERNO,'' as Description,0 as sl from EMPL_RFID union  select distinct EMA_PERNO as EMA_PERNO,(EMA_PERNO + ' - '+EMA_ENAME) as Description,1 as sl from SAPHRDB.dbo.T_EMPL_ALL where  EMA_DEPT_DESC='" + DepartmentID.SelectedValue + "' order by sl,EMA_PERNO"));
     }
     Pno.DataBind();
 }
