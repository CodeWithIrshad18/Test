protected void btnSave_Click(object sender, EventArgs e)
        {
            ApprovingAuth_Record.UnbindData();
            DataTable dt = new DataTable();
            dt.Columns.Add("ID", typeof(Guid));
            dt.Columns.Add("Approving_Authority", typeof(string));
            dt.Columns.Add("CreatedBy", typeof(string));
            dt.Columns.Add("CreatedOn", typeof(DateTime));
            CheckBoxList chk = (CheckBoxList)ApprovingAuth_Record.Rows[0].FindControl("Approving_Authority");
            foreach (ListItem item in chk.Items)
            {
                if (item.Selected)
                {
                    DataRow dr = dt.NewRow();
                    dr["ID"] = Guid.NewGuid();
                    dr["Approving_Authority"] = item.Value;
                    dr["CreatedBy"] = Session["UserName"].ToString();
                    dr["CreatedOn"] = DateTime.Now;

                    dt.Rows.Add(dr);
                }
            }
            if (dt.Rows.Count == 0)
            {
                ScriptManager.RegisterStartupScript(
                    this, GetType(), "alert", "alert('Please select Approving Authority');", true);
                return;
            }
             PageRecordDataSet.Tables["App_Approving_Authority_Master"].Clear();
            PageRecordDataSet.Tables["App_Approving_Authority_Master"].Merge(dt);
             bool result = Save();
            if (result)
            {
                GetRecords(GetFilterCondition(), ApprovingAuth_Records.PageSize, 0, "");
                ApprovingAuth_Records.BindData();
                PageRecordDataSet.Clear();
                ApprovingAuth_Record.BindData();
                btnSave.Visible = false;
                btnDelete.Visible = false;
                MyMsgBox.show(FNAAPP.Control.MyMsgBox.MessageType.Success, "Record saved successfully !");
            }
            else
            {
                MyMsgBox.show(FNAAPP.Control.MyMsgBox.MessageType.Errors, "Error while saving !");


            }


        }
