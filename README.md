<div class="row justify-content-center mt-4">
    <div class="col-md-9">

        <div class="card shadow-sm">
            <div class="card-body">

                <div class="row align-items-center">

                    <!-- DOWNLOAD -->
                    <div class="col-md-6 text-center border-right">
                        <a href="../../img/Format.xlsx" style="text-decoration:none;">
                            <div class="p-3">
                                <div class="text-danger font-weight-bold" style="font-size:18px;">
                                    Download Excel Format
                                </div>
                                <img src="../../img/excel.png"
                                     style="width:42px;height:42px;margin-top:8px;" />
                                <div class="text-muted mt-1" style="font-size:13px;">
                                    Use this format to upload data
                                </div>
                            </div>
                        </a>
                    </div>

                    <!-- UPLOAD -->
                    <div class="col-md-6">
                        <div class="text-center font-weight-bold text-success mb-2">
                            Upload Filled Excel File
                        </div>

                        <div class="d-flex justify-content-center align-items-center">

                            <!-- Hidden real file upload -->
                            <asp:FileUpload ID="FileUpload" runat="server"
                                CssClass="d-none" />

                            <!-- Fake choose file -->
                            <label for="FileUpload" class="btn btn-outline-secondary btn-sm mb-0">
                                Choose Excel
                            </label>

                            <span id="fileName" class="ml-2 text-muted" style="font-size:13px;">
                                No file chosen
                            </span>

                            <asp:Button ID="btnUpload" runat="server"
                                Text="Upload"
                                CssClass="btn btn-primary btn-sm ml-3" />
                        </div>

                        <div class="text-muted text-center mt-1" style="font-size:13px;">
                            Only .xlsx files allowed
                        </div>
                    </div>

                </div>

            </div>
        </div>

    </div>
</div>







<div class="row justify-content-center mt-4">
    <div class="col-md-8">

        <div class="card shadow-sm">
            <div class="card-body text-center">

                <div class="row align-items-center">

                    <!-- DOWNLOAD SECTION -->
                    <div class="col-md-6 mb-3 mb-md-0">
                        <a href="../../img/Format.xlsx" style="text-decoration:none;">
                            <div class="p-3 border rounded">
                                <div class="text-danger font-weight-bold" style="font-size:18px;">
                                    Download Excel Format
                                </div>
                                <img src="../../img/excel.png"
                                     alt="Download"
                                     title="Download"
                                     style="width:40px;height:40px;margin-top:8px;" />
                            </div>
                        </a>
                        <small class="text-muted d-block mt-1">
                            Use this format to upload data
                        </small>
                    </div>

                    <!-- UPLOAD SECTION -->
                    <div class="col-md-6">
                        <label class="font-weight-bold text-success" style="font-size:16px;">
                            Upload Filled Excel File
                        </label>
                        <asp:FileUpload 
                            ID="FileUpload" 
                            runat="server" 
                            CssClass="form-control-file mt-2" />
                        <small class="text-muted d-block mt-1">
                            Only .xlsx files allowed
                        </small>
                    </div>

                </div>

            </div>
        </div>

    </div>
</div>






SELECT  
    km.KPICode,
    km.KPIDetails AS Areas,
    km.Division,
    km.Department,
    u.UnitCode AS UoM,

    tsd.TargetValue AS JanuaryTarget,
    kd.Value AS JanuaryActual

FROM App_KPIMaster_NOPR km

LEFT JOIN App_TargetSetting_NOPR ts
    ON ts.KPIID = km.ID

LEFT JOIN App_TargetSettingDetails_NOPR tsd
    ON tsd.MasterID = ts.ID
   AND tsd.PeriodicityTransactionID = 'JAN'   -- 🔥 month filter here

LEFT JOIN App_PeriodicityTransaction_NOPR pt
    ON pt.PeriodicityName = tsd.PeriodicityTransactionID
   AND pt.PeriodicityID = ts.PeriodicityID   -- 🔥 same as old query

LEFT JOIN App_KPIDetails_NOPR kd
    ON kd.KPIID = km.ID
   AND kd.PeriodTransactionID = pt.ID        -- 🔥 EXACT SAME actual mapping

LEFT JOIN App_UOM_NOPR u
    ON km.UnitID = u.ID

WHERE km.Department = 'Human Resource & Industrial Relations'

ORDER BY km.KPICode;





SELECT  
    km.KPICode,
    km.KPIDetails,
    km.Division,
    km.Department,
    km.Section,
    t.TypeofKPI,
    u.UnitCode,
    p.PeriodicityName,
    pt.PeriodicityName AS Month,
    tsd.TargetValue,
    kd.Value AS ActualValue,
    kd.YTDValue
FROM App_KPIMaster_NOPR km
LEFT JOIN App_TargetSetting_NOPR ts ON ts.KPIID = km.ID
LEFT JOIN App_UOM_NOPR u ON km.UnitID = u.ID
LEFT JOIN App_PeriodicityMaster_NOPR p ON km.PeriodicityID = p.ID
LEFT JOIN App_TargetSettingDetails_NOPR tsd ON tsd.MasterID = ts.ID
LEFT JOIN App_TypeofKPI_NOPR t ON km.TypeofKPIID = t.ID
LEFT JOIN App_PeriodicityTransaction_NOPR pt 
       ON pt.PeriodicityName = tsd.PeriodicityTransactionID
      AND pt.PeriodicityID = ts.PeriodicityID
LEFT JOIN App_KPIDetails_NOPR kd
       ON kd.KPIID = km.ID
      AND kd.PeriodTransactionID = pt.ID
WHERE (@KpiId IS NULL OR km.ID = @KpiId)
ORDER BY km.KPICode,pt.Sl_no;




SELECT  
    km.KPICode,
    km.KPIDetails AS Areas,
    km.Division,
    km.Department,
    u.UnitCode AS UoM,

    tsd.TargetValue AS JanuaryTarget,
    kd.Value AS JanuaryActual

FROM App_KPIMaster_NOPR km

INNER JOIN App_TargetSetting_NOPR ts
    ON ts.KPIID = km.ID

INNER JOIN App_TargetSettingDetails_NOPR tsd
    ON tsd.MasterID = ts.ID
   AND tsd.PeriodicityTransactionID = 'JAN'   

INNER JOIN App_PeriodicityTransaction_NOPR pt
    ON pt.PeriodicityName = tsd.PeriodicityTransactionID
   AND pt.PeriodicityID = ts.PeriodicityID   

LEFT JOIN App_KPIDetails_NOPR kd
    ON kd.KPIID = km.ID
   AND kd.PeriodTransactionID = pt.ID        

LEFT JOIN App_UOM_NOPR u
    ON km.UnitID = u.ID

WHERE km.Department = 'Human Resource & Industrial Relations'

ORDER BY km.KPICode;
