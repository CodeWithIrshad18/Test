 PageRecordDataSet.Tables["App_Attendancedetails1"].AcceptChanges();

                    // start Sangita 7/21/2025
                    string Inactive_FormC3_WoNo = string.Empty;
                    string failedWorkorderstring = string.Empty;
                    List<string> validWorkOrders = new List<string>();
                    List<string> failedWorkorder = new List<string>();
                    HashSet<string> overCountWarnings = new HashSet<string>();
                    
                    List<string> FormC3_WoNo = new List<string>();
                    
                    

                    // Step 1: Get distinct WorkOrderNo
                    DataView workOrderView = new DataView(PageRecordDataSet.Tables["App_AttendanceDetails"]);
                    DataTable distinctWorkOrders = workOrderView.ToTable(true, "WorkOrderNo");



                    foreach (DataRow woRow in distinctWorkOrders.Rows)
                    {
                        string workOrder = woRow["WorkOrderNo"].ToString();

                        // Step 2: Get expected category-wise count from Ds2
                        DataSet Ds2 = blobj.Get_WoNo_Chk(workOrder);
                        if (Ds2 != null && Ds2.Tables.Count > 0 && Ds2.Tables[0].Rows.Count > 0)
                        {
                            //// Step 3: Filter PageRecordDataSet for current WorkOrderNo
                            DataView filteredView = new DataView(PageRecordDataSet.Tables["App_AttendanceDetails"]);
                            filteredView.RowFilter = $"WorkOrderNo = '{workOrder}'";

                            // Step 4: Get distinct AadharNo and WorkManCategory for current WorkOrder
                            DataTable distinctWorkmen = filteredView.ToTable(true, "AadharNo", "WorkManCategory");

                            var categoryCount = (from r in distinctWorkmen.AsEnumerable()
                                                 let cat = r["WorkManCategory"].ToString().Trim().ToUpper()
                                                 where !string.IsNullOrEmpty(cat)
                                                 group r by cat into g
                                                 select new
                                                 {
                                                     Key = g.Key,
                                                     Count = g.Count()
                                                 }).ToDictionary(x => x.Key, x => x.Count);


                            foreach (DataRow row in Ds2.Tables[0].Rows)
                            {
                                string category = row["EMP_TYPE"].ToString().Trim().ToUpper();  // Normalize
                                int requiredCount = 0;

                                // Safe parse for Total column
                                if (row["Total"] != DBNull.Value && !string.IsNullOrWhiteSpace(row["Total"].ToString()))
                                {
                                    int.TryParse(row["Total"].ToString(), out requiredCount);
                                }

                                
                                int actualCount = categoryCount.ContainsKey(category) ? categoryCount[category] : 0;

                                // Debug print (optional)
                                Console.WriteLine($"Checking category: {category}, Required: {requiredCount}, Actual: {actualCount}");

                                if (actualCount > requiredCount)
                                {
                                    


                                    failedWorkorder.Add(workOrder);
                                    overCountWarnings.Add($"{workOrder}|{category}|{actualCount}|{requiredCount}");
                                }
                            }

                        }


                        string C3_CLOSER_DATE = PageRecordDataSet.Tables["App_Attendancedetails"].Rows[0]["Dates"].ToString();
                        string V_CODE = PageRecordDataSet.Tables["App_Attendancedetails"].Rows[0]["VendorCode"].ToString();
                        
                        DataSet Ds3 = blobj.FromC3_WoNo_Chk_(C3_CLOSER_DATE, V_CODE, workOrder);

                        if (Ds3.Tables[0].Rows.Count > 0)
                        {
                            

                        }
                        else
                        {
                            FormC3_WoNo.Add(workOrder);
                            
                        }

                    }

                    Inactive_FormC3_WoNo = string.Join(", ", FormC3_WoNo);
                    failedWorkorderstring = string.Join(", ", overCountWarnings);

                    if(FormC3_WoNo.Count>0)
                    {
                        MyMsgBox.show(CLMS.Control.MyMsgBox.MessageType.Errors, "These Workorders : " + Inactive_FormC3_WoNo + "is not present in Form C3!!!");

                    }


                    if (overCountWarnings.Count > 0)
                    {
                        string msg = $"Mismatches of No's of workers in worker's attendance count date: {str_date_to} against the workorder No is found in Form C3:<br><br>";
                        msg += "<table border='1' cellpadding='4' cellspacing='0' style='border-collapse:collapse;'>";
                        msg += "<tr style='background-color:#f2f2f2;'><th>WorkOrder</th><th>Category</th><th>Count Of WorkMan in Attendance</th><th>Count Of WorkMan in Form C3</th></tr>";

                        foreach (string item in overCountWarnings)
                        {
                            string[] parts = item.Split('|');
                            if (parts.Length == 4)
                            {
                                msg += $"<tr><td>{parts[0]}</td><td>{parts[1]}</td><td>{parts[2]}</td><td>{parts[3]}</td></tr>";
                            }
                        }

                        msg += "</table>";

                        MyMsgBox.show(CLMS.Control.MyMsgBox.MessageType.Errors, msg);



                        
                    }

                    

                    else
                    {


                        // end

                        DataSet entries_count = new DataSet();
                        entries_count = blobj.getattendance_entries_count(v_code, mm_1.PadLeft(2, '0'), yyyy_1);
                        entries_count.Tables[0].PrimaryKey = new DataColumn[] { entries_count.Tables[0].Columns["AadharNo"] };

                        //PageRecordDataSet.Tables["App_Attendancedetails"].PrimaryKey = new DataColumn[] { PageRecordDataSet.Tables["App_Attendancedetails"].Columns["AadharNo"] };

                        string print_adhar = "";

                        for (int i = 0; i < PageRecordDataSet.Tables["App_Attendancedetails"].Rows.Count; i++)
                        {

                            DataRow dr1 = PageRecordDataSet.Tables["App_Attendancedetails"].NewRow();
                            string[] array = new string[1];
                            array[0] = PageRecordDataSet.Tables["App_Attendancedetails"].Rows[i]["AadharNo"].ToString();

                            dr1 = entries_count.Tables[0].Rows.Find(array);
                            if (dr1 != null)
                            {
                                print_adhar = print_adhar + "," + PageRecordDataSet.Tables["App_Attendancedetails"].Rows[i]["AadharNo"].ToString();



                            }



                        }

                        if (print_adhar == "")

                        {

                            //blobj.delete_attendance(v_code, str_date_to); // to delete attendance of TO date
                            //bool result = Save();
                            //if (result)
                            //{
                            //    PageRecordDataSet.Clear();
                            //    MyMsgBox.show(CLMS.Control.MyMsgBox.MessageType.Success, "Record Copied Successfully !");
                            //}
                            //else
                            //{
                            //    MyMsgBox.show(CLMS.Control.MyMsgBox.MessageType.Errors, " Error while Copying !!! ");
                            //}
                        }
                        else
                        {
                            MyMsgBox.show(CLMS.Control.MyMsgBox.MessageType.Errors, "Below Adhar numbers have more than required entries of the selected month in attendance record, kindly check !!!  " + print_adhar);
                        }

                    }
                } // end 

                else
                {
                    MyMsgBox.show(CLMS.Control.MyMsgBox.MessageType.Errors, " Error while Copying!!! , No data found against the selected from date. ");
                }

            }
            else {
                MyMsgBox.show(CLMS.Control.MyMsgBox.MessageType.Errors, " Wage Compliance has been submitted , can not modify attendance !!! ");
            }



see this is my full code when FormC3_WoNo.Count>0 i want to display message and stop to save   and also when  overCountWarnings.Count > 0 meassage disply and stop to save how to set this code to disply both  message one by on when both conditions matches
 if(FormC3_WoNo.Count>0)
 

if (overCountWarnings.Count > 0)
   
