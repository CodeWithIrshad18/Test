       public void StoreData(string ddMMyy, string tmIn, string tmOut, string Pno)
       {
           using (var connection = new SqlConnection(configuration.GetConnectionString("RFID")))
           {
               connection.Open();

               if (!string.IsNullOrEmpty(tmIn))
               {
                   var query = @"
               INSERT INTO T_TRBDGDAT_EARS(TRBDGDA_BD_DATE, TRBDGDA_BD_TIME, TRBDGDA_BD_INOUT, TRBDGDA_BD_READER,
               TRBDGDA_BD_CHKHS, TRBDGDA_BD_SUBAREA,TRBDGDA_BD_ENTRYUID, TRBDGDA_BD_PNO) 
               VALUES 
               (@TRBDGDA_BD_DATE,
               @TRBDGDA_BD_TIME, 
               @TRBDGDA_BD_INOUT,
               @TRBDGDA_BD_READER, 
               @TRBDGDA_BD_CHKHS, 
               @TRBDGDA_BD_SUBAREA, 
               @TRBDGDA_BD_ENTRYUID, 
               @TRBDGDA_BD_PNO)";

                   var parameters = new
                   {
                       TRBDGDA_BD_DATE = ddMMyy,
                       TRBDGDA_BD_TIME = ConvertTimeToMinutes(tmIn),
                       TRBDGDA_BD_INOUT = "I",
                       TRBDGDA_BD_READER = "2",
                       TRBDGDA_BD_CHKHS = "2",
                       TRBDGDA_BD_SUBAREA = "JUSC12",
                       TRBDGDA_BD_ENTRYUID = "MOBILE",
                       TRBDGDA_BD_PNO = Pno
                   };

                   connection.Execute(query, parameters);

                   var Punchquery = @"
               INSERT INTO T_TRPUNCHDATA_EARS(PDE_PUNCHDATE,PDE_PUNCHTIME,PDE_INOUT,PDE_MACHINEID,
               PDE_READERNO,PDE_CHKHS,PDE_SUBAREA,PDE_ENTRYUID,PDE_PSRNO) 
               VALUES 
               (@PDE_PUNCHDATE,
               @PDE_PUNCHTIME, 
               @PDE_INOUT,
               @PDE_MACHINEID, 
               @PDE_READERNO, 
               @PDE_CHKHS, 
               @PDE_SUBAREA, 
               @PDE_ENTRYUID,
               @PDE_PSRNO)";

                   var parameters2 = new
                   {
                       PDE_PUNCHDATE = ddMMyy,
                       PDE_PUNCHTIME = tmIn,
                       PDE_INOUT = "I",
                       PDE_MACHINEID = "2",
                       PDE_READERNO = "2",
                       PDE_CHKHS = "2",
                       PDE_SUBAREA = "JUSC12",
                       PDE_ENTRYUID = "MOBILE",
                       PDE_PSRNO = Pno
                   };

                   connection.Execute(Punchquery, parameters2);
               }

               if (!string.IsNullOrEmpty(tmOut))
               {
                   var queryOut = @"
               INSERT INTO T_TRBDGDAT_EARS(TRBDGDA_BD_DATE, TRBDGDA_BD_TIME, TRBDGDA_BD_INOUT, TRBDGDA_BD_READER, 
                TRBDGDA_BD_CHKHS, TRBDGDA_BD_SUBAREA,TRBDGDA_BD_ENTRYUID, TRBDGDA_BD_PNO) 
               VALUES 
               (@TRBDGDA_BD_DATE,
               @TRBDGDA_BD_TIME, 
               @TRBDGDA_BD_INOUT, 
               @TRBDGDA_BD_READER, 
               @TRBDGDA_BD_CHKHS,
               @TRBDGDA_BD_SUBAREA,
               @TRBDGDA_BD_ENTRYUID,
               @TRBDGDA_BD_PNO)";

                   var parametersOut = new
                   {
                       TRBDGDA_BD_DATE = ddMMyy,
                       TRBDGDA_BD_TIME = ConvertTimeToMinutes(tmOut),
                       TRBDGDA_BD_INOUT = "O",
                       TRBDGDA_BD_READER = "2",
                       TRBDGDA_BD_CHKHS = "2",
                       TRBDGDA_BD_SUBAREA = "JUSC12",
                       TRBDGDA_BD_ENTRYUID = "MOBILE",
                       TRBDGDA_BD_PNO = Pno
                   };

                   connection.Execute(queryOut, parametersOut);

                   var Punchquery = @"
               INSERT INTO T_TRPUNCHDATA_EARS(PDE_PUNCHDATE,PDE_PUNCHTIME,PDE_INOUT,PDE_MACHINEID,
               PDE_READERNO,PDE_CHKHS,PDE_SUBAREA,PDE_ENTRYUID,PDE_PSRNO) 
               VALUES 
               (@PDE_PUNCHDATE,
               @PDE_PUNCHTIME, 
               @PDE_INOUT,
               @PDE_MACHINEID, 
               @PDE_READERNO, 
               @PDE_CHKHS, 
               @PDE_SUBAREA,
               @PDE_ENTRYUID,
               @PDE_PSRNO)";

                   var parameters2 = new
                   {
                       PDE_PUNCHDATE = ddMMyy,
                       PDE_PUNCHTIME = tmOut,
                       PDE_INOUT = "O",
                       PDE_MACHINEID = "2",
                       PDE_READERNO = "2",
                       PDE_CHKHS = "2",
                       PDE_SUBAREA = "JUSC12",
                       PDE_ENTRYUID = "MOBILE",
                       PDE_PSRNO = Pno
                   };

                   connection.Execute(Punchquery, parameters2);
               }

           

           }
       }

       public void StoreDataNOPR(string ddMMyy, string tmIn, string tmOut, string Pno)
       {
           using (var connection = new SqlConnection(configuration.GetConnectionString("RFID")))
           {
               connection.Open();

               if (!string.IsNullOrEmpty(tmIn))
               {
                   var query = @"
               INSERT INTO PUNCHDATA(EMP_CODE, PUNCHDATE, PUNCHTIME, INOUT,INSERT_DATE,INSERT_TIME) 
               VALUES 
               (@EMP_CODE,
               @PUNCHDATE, 
               @PUNCHTIME,
               @INOUT,
               @INSERT_DATE,
               @INSERT_TIME)";

                   var parameters = new
                   {
                       EMP_CODE = Pno,
                       PUNCHDATE = DateTime.Now.ToString("yyyy-MM-dd"),
                       PUNCHTIME = DateTime.Now.ToString("HH:mm") + ":00",
                       INOUT = "I",
                       INSERT_DATE = DateTime.Now.ToString("yyyy-MM-dd"),
                       INSERT_TIME = DateTime.Now.ToString("HH:mm")
                   };

                   connection.Execute(query, parameters);
               }

               if (!string.IsNullOrEmpty(tmOut))
               {
                   var queryOut = @"
               INSERT INTO PUNCHDATA(EMP_CODE, PUNCHDATE, PUNCHTIME, INOUT,INSERT_DATE,INSERT_TIME) 
               VALUES 
               (@EMP_CODE,
               @PUNCHDATE, 
               @PUNCHTIME,
               @INOUT,
               @INSERT_DATE,
               @INSERT_TIME)";

                   var parameters = new
                   {
                       EMP_CODE = Pno,
                       PUNCHDATE = DateTime.Now.ToString("yyyy-MM-dd"),
                       PUNCHTIME = DateTime.Now.ToString("HH:mm")+":00",
                       INOUT = "O",
                       INSERT_DATE = DateTime.Now.ToString("yyyy-MM-dd"),
                       INSERT_TIME = DateTime.Now.ToString("HH:mm")
                   };

                   connection.Execute(queryOut, parameters);
               }

           }

           }

       public void StoreVersionDetails(string Pno,string tmIn, string tmOut)
       {
           using (var connection = new SqlConnection(configuration.GetConnectionString("RFID")))
           {
               connection.Open();

            
               if (!string.IsNullOrEmpty(tmIn))
               {
                   var query = @"
               INSERT INTO App_VersionDetail (Pno, Version, PunchIN)
               VALUES (@Pno, @Version, @PunchIN)";

                   var parameters = new
                   {
                       Pno,
                       Version = "OLD VERSION",
                       PunchIN = "I"
                   };

                   connection.Execute(query, parameters);
               }

          
               if (!string.IsNullOrEmpty(tmOut))
               {
                   var query = @"
               INSERT INTO App_VersionDetail (Pno, Version, PunchOUT)
               VALUES (@Pno, @Version, @PunchOUT)";

                   var parameters = new
                   {
                       Pno,
                       Version = "OLD VERSION",
                       PunchOUT = "O"
                   };

                   connection.Execute(query, parameters);
               }
           }
       }
