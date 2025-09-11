public List<Dictionary<string, object>> Leave_details(string WorkOrder, string VendorCode)
{
    using (SqlConnection con = new SqlConnection("Data Source=10.0.168.50;Initial Catalog=CLMSDB;User ID=fs;Password=p@ssW0Rd321;TrustServerCertificate=True"))
    {
        con.Open();

        List<Dictionary<string, object>> result = null;

        string query1 = @"
            select FORMAT(GETDATE(),'dd-MM-yyyy') as CURR_MONTH,tab.Leave_Year,tab.WorkOrder,tab.VendorCode,
                   VM.V_NAME as VendorName,tab.Leave_compliance,
                   FORMAT(VW.START_DATE,'dd-MM-yyyy') as start_date,
                   FORMAT(VW.END_DATE,'dd-MM-yyyy') as end_date 
            from (
                 select distinct D.V_Code as VendorCode, D.year as Leave_Year, D.WorkOrderNo as WorkOrder,
                        ISNULL(case when S.Status = 'Request Closed' then 'Y' else 'N' end,'N') as Leave_compliance
                 from App_Leave_Comp_Details D
                 left join App_Leave_Comp_Summary S on S.ID = D.MasterID
                 where D.WorkOrderNo = @WorkOrder and D.V_Code = @VendorCode
                 union
                 select distinct right(V_CODE,5) as VendorCode, LEAVE_YEAR as Leave_Year, WO_NO as WorkOrder, 'Y' as Leave_compliance
                 from JCMS_ONLINE_TEMP_LEAVE
                 where STATUS = 'Approved' and WO_NO = @WorkOrder and right(V_CODE,5) = @VendorCode
                 union
                 select right(V_CODE,5) as VendorCode, LEFT(proc_month,4) as Leave_Year, WO_NO as WorkOrder, 'Y' as Leave_compliance
                 from JCMS_C_ENTRY_DETAILS
                 where C_NO = '7' and WO_NO = @WorkOrder and right(V_CODE,5) = @VendorCode
            ) tab
            left join App_Vendorwodetails VW on VW.WO_NO = tab.WorkOrder
            left join App_VendorMaster VM on VM.V_CODE = tab.VendorCode
            order by tab.Leave_Year";

        string fallbackQuery = @"
            select V_CODE, WO_NO, START_DATE, END_DATE
            from App_Vendorwodetails
            where START_DATE >= '2021-01-01 00:00:00.000'
              and WO_NO not in (select WO_NO from COMPLIANCE_DBTS)
            order by V_CODE";

        SqlDataAdapter da = new SqlDataAdapter(query1, con);
        da.SelectCommand.Parameters.AddWithValue("@WorkOrder", WorkOrder);
        da.SelectCommand.Parameters.AddWithValue("@VendorCode", VendorCode);

        DataSet ds = new DataSet();
        da.SelectCommand.CommandTimeout = 300;
        da.Fill(ds);

        if (ds.Tables[0].Rows.Count == 0) // <-- if no records found, run fallback query
        {
            da = new SqlDataAdapter(fallbackQuery, con);
            ds = new DataSet();
            da.Fill(ds);
        }

        result = ConvertDataTableToDictionaryList(ds.Tables[0]);
        return result;
    }
}

 
 
 
 public List<Dictionary<string, object>> Leave_details(string WorkOrder, string VendorCode)
 {
     SqlConnection con = new SqlConnection("Data Source=10.0.168.50;Initial Catalog=CLMSDB;User ID=fs;Password=p@ssW0Rd321;TrustServerCertificate=True");
     con.Open();

     List<Dictionary<string, object>> result = null;


     SqlDataAdapter da = new SqlDataAdapter("  select FORMAT(GETDATE(),'dd-MM-yyyy') as CURR_MONTH,tab.Leave_Year,tab.WorkOrder,tab.VendorCode,VM.V_NAME as VendorName,tab.Leave_compliance, FORMAT(VW.START_DATE,'dd-MM-yyyy') as start_date,FORMAT(VW.END_DATE,'dd-MM-yyyy') as end_date from (" +
         " select distinct D.V_Code as VendorCode, D.year as Leave_Year, D.WorkOrderNo as WorkOrder, ISNULL(case when S.Status = 'Request Closed' then 'Y' else 'N' end,'N') as Leave_compliance " +
         "  from App_Leave_Comp_Details D " +
         "  left  join App_Leave_Comp_Summary S on S.ID = D.MasterID   where D.WorkOrderNo = '" + WorkOrder + "'  and D.V_Code = '" + VendorCode + "' " +
         " union  " +

         "   select  distinct right(V_CODE,5) as VendorCode,LEAVE_YEAR as Leave_Year,WO_NO as WorkOrder,'Y' as Leave_compliance  " +
         "  from JCMS_ONLINE_TEMP_LEAVE where " +
          "  STATUS = 'Approved' and  " +
          "    WO_NO = '" + WorkOrder + "' and right(V_CODE,5)= '" + VendorCode + "'  " +

          "  union   " +

          "  select RIGHT(V_CODE, 5) as VendorCode,LEFT(proc_month, 4) as Leave_Year,WO_NO as WorkOrder,'Y' as Leave_compliance from JCMS_C_ENTRY_DETAILS where C_NO = '7' and WO_NO = '" + WorkOrder + "' and RIGHT(V_CODE,5)= '" + VendorCode + "'  " +
         "   )tab  " +
         "   left join App_Vendorwodetails VW on VW.WO_NO = tab.WorkOrder  " +
         "   left join App_VendorMaster VM on VM.V_CODE = tab.VendorCode  order by tab.Leave_Year ", con);
     //SqlDataAdapter da = new SqlDataAdapter("select  V_CODE,WO_NO,START_DATE,END_DATE from App_Vendorwodetails where START_DATE>='2021-01-01 00:00:00.000' and WO_NO not in (select WO_NO from COMPLIANCE_DBTS) order by V_CODE ", con);
     DataSet ds = new DataSet();
     da.SelectCommand.CommandTimeout = 300;
     da.Fill(ds);

     result = ConvertDataTableToDictionaryList(ds.Tables[0]);
     //TraceService("Api Executed Successfully and End At :-" + System.DateTime.Now);
     con.Close();
     return result;

 }

in my ds when data is not found or null then pls execute this query
