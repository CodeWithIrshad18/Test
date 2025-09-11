public List<Dictionary<string, object>> Bonus_details(string WorkOrder, string VendorCode)
{
    using (SqlConnection con = new SqlConnection("Data Source=10.0.168.50;Initial Catalog=CLMSDB;User ID=fs;Password=p@ssW0Rd321;TrustServerCertificate=True"))
    {
        con.Open();

        List<Dictionary<string, object>> result = null;

        string query1 = @"
            select FORMAT(GETDATE(),'dd-MM-yyyy') as CURR_MONTH,
                   tab.BONUS_YEAR,
                   tab.WorkOrder,
                   tab.VendorCode,
                   VM.V_NAME as VendorName,
                   tab.Bonus_compliance,
                   FORMAT(VW.START_DATE,'dd-MM-yyyy') as start_date,
                   FORMAT(VW.END_DATE,'dd-MM-yyyy') as end_date
            from (
                select distinct D.Vcode as VendorCode,
                                D.year as BONUS_YEAR,
                                D.WorkOrderNo as WorkOrder,
                                ISNULL(case when S.Status = 'Request Closed' then 'Y' else 'N' end,'N') as Bonus_compliance
                from App_Bonus_Comp_Details D
                left join App_Bonus_Comp_Summary S on S.ID = D.MasterID
                where D.WorkOrderNo = '" + WorkOrder + @"' and D.Vcode = '" + VendorCode + @"'

                union

                select distinct right(V_CODE,5) as VendorCode,
                                BONUS_YEAR as BONUS_YEAR,
                                WO_NO as WorkOrder,
                                'Y' as Bonus_compliance
                from JCMS_ONLINE_TEMP_BONUS
                where STATUS = 'Approved'
                  and WO_NO = '" + WorkOrder + @"'
                  and right(V_CODE,5) = '" + VendorCode + @"'

                union

                select RIGHT(V_CODE,5) as VendorCode,
                       LEFT(proc_month,4) as BONUS_YEAR,
                       WO_NO as WorkOrder,
                       'Y' as Bonus_compliance
                from JCMS_C_ENTRY_DETAILS
                where C_NO = '8'
                  and WO_NO = '" + WorkOrder + @"'
                  and RIGHT(V_CODE,5) = '" + VendorCode + @"'
            ) tab
            left join App_Vendorwodetails VW on VW.WO_NO = tab.WorkOrder
            left join App_VendorMaster VM on VM.V_CODE = tab.VendorCode
            order by tab.BONUS_YEAR";

        string query2 = @"
            select FORMAT(GETDATE(),'dd-MM-yyyy') as CURR_MONTH,
                   'All' as BONUS_YEAR,
                   VW.WO_NO as WorkOrder,
                   VW.V_CODE as VendorCode,
                   VM.V_NAME as VendorName,
                   'N' as Bonus_compliance,
                   FORMAT(VW.START_DATE,'dd-MM-yyyy') as start_date,
                   FORMAT(VW.END_DATE,'dd-MM-yyyy') as end_date
            from App_Vendorwodetails VW
            left join App_VendorMaster VM on VW.V_CODE = VM.V_CODE
            where VW.WO_NO = '" + WorkOrder + @"'
              and VW.V_CODE = '" + VendorCode + @"'";

        SqlDataAdapter da = new SqlDataAdapter(query1, con);
        da.SelectCommand.CommandTimeout = 300;

        DataSet ds = new DataSet();
        da.Fill(ds);

        if (ds.Tables[0].Rows.Count == 0) // <-- no rows, so run fallback
        {
            da = new SqlDataAdapter(query2, con);
            da.SelectCommand.CommandTimeout = 300;
            ds = new DataSet();
            da.Fill(ds);
        }

        result = ConvertDataTableToDictionaryList(ds.Tables[0]);
        return result;
    }
}

  
  
  
  public List<Dictionary<string, object>> Bonus_details(string WorkOrder, string VendorCode)
  {
      SqlConnection con = new SqlConnection("Data Source=10.0.168.50;Initial Catalog=CLMSDB;User ID=fs;Password=p@ssW0Rd321;TrustServerCertificate=True");
      con.Open();


      List<Dictionary<string, object>> result = null;


      SqlDataAdapter da = new SqlDataAdapter("  select FORMAT(GETDATE(),'dd-MM-yyyy') as CURR_MONTH,tab.BONUS_YEAR,tab.WorkOrder,tab.VendorCode,VM.V_NAME as VendorName,tab.Bonus_compliance, FORMAT(VW.START_DATE,'dd-MM-yyyy') as start_date,FORMAT(VW.END_DATE,'dd-MM-yyyy') as end_date from (  " +
      " select distinct D.Vcode as VendorCode, D.year as BONUS_YEAR, D.WorkOrderNo as WorkOrder, ISNULL(case when S.Status = 'Request Closed' then 'Y' else 'N' end,'N') as Bonus_compliance  " +
      "   from App_Bonus_Comp_Details D  " +
      "   left  " +
      "   join App_Bonus_Comp_Summary S on S.ID = D.MasterID  " +
      "   where D.WorkOrderNo = '" + WorkOrder + "'  and D.Vcode = '" + VendorCode + "'  " +
      "   union   " +
      "   select  distinct right(V_CODE,5) as VendorCode,BONUS_YEAR as BONUS_YEAR,WO_NO as WorkOrder,'Y' as Bonus_compliance  " +
      "  from JCMS_ONLINE_TEMP_BONUS where " +
      "   STATUS = 'Approved' and  " +
      "     WO_NO = '" + WorkOrder + "' and right(V_CODE,5)= '" + VendorCode + "'  " +
      "   union  " +
      "   select RIGHT(V_CODE, 5) as VendorCode,LEFT(proc_month, 4) as BONUS_YEAR,WO_NO as WorkOrder,'Y' as Bonus_compliance from JCMS_C_ENTRY_DETAILS where C_NO = '8' and WO_NO = '" + WorkOrder + "' and RIGHT(V_CODE,5)= '" + VendorCode + "'  " +
      "   )tab   " +
      "   left join App_Vendorwodetails VW on VW.WO_NO = tab.WorkOrder   " +
      "   left join App_VendorMaster VM on VM.V_CODE = tab.VendorCode  " +
      "  order by tab.BONUS_YEAR  ", con);
     
      DataSet ds = new DataSet();
      da.SelectCommand.CommandTimeout = 300;
      da.Fill(ds);

      result = ConvertDataTableToDictionaryList(ds.Tables[0]);
     
      con.Close();
      return result;

  }




in this if ds data is not found or null then pls execute this another query    


 select FORMAT(GETDATE(),'dd-MM-yyyy') as CURR_MONTH, 'All' as BONUS_YEAR,VW.WO_NO as WorkOrder,  VW.V_CODE as VendorCode,   VM.V_NAME as VendorName,  'N' as Bonus_compliance,   FORMAT(VW.START_DATE,'dd-MM-yyyy') as start_date,  FORMAT(VW.END_DATE,'dd-MM-yyyy') as end_date   from App_Vendorwodetails VW left join App_VendorMaster VM on VW.V_CODE=VM.V_CODE  where VW.WO_NO='4700026842' and VW.V_CODE='15832'
