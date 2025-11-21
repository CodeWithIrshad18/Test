  public DataSet Get_ChkInChkOutTime(string Gcode)
        {

            string strsql = "select Left(Code_Desc,CHARINDEX('-',Code_Desc)-1) as checkIn,SUBSTRING(Code_Desc,CHARINDEX('-',Code_Desc) + 1,LEN(Code_Desc)) as checkOut from jehpdb.dbo.App_HTIME_Master where Code=@Code";
            Dictionary<string, object> objParam = new Dictionary<string, object>();
            DataHelper dh = new DataHelper();
            objParam.Add("Code", Gcode);
            return dh.GetDataset(strsql, "App_HTIME_Master", objParam);

        }
