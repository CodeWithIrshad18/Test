<div class="form-group col-md-3 mb-1">
                     <asp:Label for="lblPno" runat="server" CssClass="m-0 mr-5 p-0 col-form-label-sm  font-weight-bold fs-6">P.No:</asp:Label>
                    <asp:DropDownList ID="Pno" runat="server"  CssClass="form-control form-control-sm col-sm-8" OnSelectedIndexChanged="Pno_SelectedIndexChanged" AutoPostBack="true">   
                 </asp:DropDownList> 

                        </div>


Dictionary<string, object> ddlParams = new Dictionary<string, object>();
            
            ddlParams.Add("Department", ((DropDownList)HOD_Record.Rows[0].FindControl("Department")).SelectedValue);
            string strddlDepartment = ((DropDownList)HOD_Record.Rows[0].FindControl("Department")).SelectedValue;
            ((DropDownList)HOD_Record.Rows[0].FindControl("Department")).SelectedValue = strddlDepartment;
           
            string Dept = string.Empty;
            DataSet ds = new DataSet();
            BL_HOD_Master blobj = new BL_HOD_Master();
            ds = blobj.GetDeptHodPno(strddlDepartment);
            
            DropDownList ddlPno = (DropDownList)HOD_Record.Rows[0].FindControl("Pno");
            ddlPno.Items.Clear();
             if (ds.Tables[0].Rows.Count > 0)
            {
                ddlPno.DataSource = ds.Tables[0];
                ddlPno.DataTextField = "ema_perno";
                ddlPno.DataValueField = "ema_perno";
                ddlPno.DataBind();
                ddlPno.Items.Insert(0, "-- Select --");
                ddlPno.Enabled = true;
            }


GetRecord(HOD_Records.SelectedDataKey.Value.ToString());
            HOD_Record.DataBind();

            btnSave.Visible = true;
            btnDelete.Visible = true;
