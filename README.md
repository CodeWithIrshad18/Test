   public void RatingMail()
        {
            using (SqlConnection con = new SqlConnection("Data Source=10.0.168.30;Initial Catalog=TDMS;User ID=fs;Password=jusco@123"))
            {
                con.Open();
                string query = @"select PM.ID,ModuleID,ModuleName,(cast(FromDate as varchar(11))+' ' +FromTime) as Fromdate,cast((cast(Todate as varchar(11))+' ' +ToTime) as datetime) as Todate,Venue,NoofEmpl=(select count(*) from App_PlanDetails where planID = PM.ID ),PM.CreatedBY,PM.DepartmentID from App_PlanMaster PM inner join App_Modules md on PM.ModuleId=MD.ID  inner join App_CompetencyMaster AC on md.CompetencyID=AC.ID  where PM.CompetencyType='1' and DATEDIFF(month,cast((cast(ToDate as varchar(11))+ ' ' + ToTime) as datetime),GETDATE())>=2 ";
                SqlCommand cmd = new SqlCommand(query, con);
                DataTable dt = new DataTable();
                dt.Load(cmd.ExecuteReader());
                string id = "";
                string DeptId = "";
                
                    foreach (DataRow row in dt.Rows)
                    {
                        id = "9B6739AB-94E4-4451-B10B-24A0028AFC89";//row["ID"].ToString();
                        DeptId = "9f738fc0-0b72-44de-9c41-ff5432ec4b4a"; //row["DepartmentID"].ToString();

                    

                        string HodPno = GetHodMailDeptID(DeptId);
                        if (string.IsNullOrEmpty(HodPno)) continue;

                        string HodEmail_To = GetEMail(HodPno);
                        if (string.IsNullOrEmpty(HodEmail_To)) continue;

                        string encodedId = Convert.ToBase64String(Encoding.UTF8.GetBytes(id));
                        encodedId = HttpUtility.UrlEncode(encodedId);

                        string baseUrl = HttpContext.Current.Request.Url.GetLeftPart(UriPartial.Authority);  //  HttpContext.Current.Request.Url.GetLeftPart(UriPartial.Authority); //"http://10.0.168.30"




                        string link = baseUrl + "/TDMS/HOD_Rating/HOD_Rating.aspx?ID=" + encodedId;
                        string body = $"Dear HOD,<br/>Please submit rating.<br/><br/>Click here: <a href='{link}'>Rating Form</a>";

                        SendMail(HodEmail_To, body);

                        MarkMailSent(id);

                    }
               


                

            }


        }
