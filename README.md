<script>
    function showLoading(show) {
        if (show)
            $("#loading-overlay").css("display", "flex");
        else
            $("#loading-overlay").hide();
    }

    // ENTER KEY SUBMIT
    $(document).on("keypress", function (e) {
        if (e.which === 13) {
            $("#btnLogin").click();
        }
    });

    // CAPTCHA STYLING
    function styleCaptcha(text) {
        const colors = ["#e74c3c", "#2980b9", "#27ae60", "#8e44ad", "#d35400", "#2c3e50"];
        let html = "";

        for (let i = 0; i < text.length; i++) {
            let color = colors[Math.floor(Math.random() * colors.length)];
            let rotate = Math.floor(Math.random() * 20 - 10);

            html += `<span style="color:${color}; display:inline-block; transform:rotate(${rotate}deg);">
                        ${text[i]}
                     </span>`;
        }

        $("#captchaDisplay").html(html);
    }

    // INITIAL CAPTCHA LOAD
    document.addEventListener("DOMContentLoaded", function () {
        styleCaptcha($("#serverCaptcha").val());
    });

    // REFRESH CAPTCHA
    function refreshCaptcha() {
        fetch(window.appRoot + 'User/RefreshCaptcha')
            .then(res => res.json())
            .then(data => {
                $("#serverCaptcha").val(data.captcha);
                styleCaptcha(data.captcha);
            });
    }

    // LOGIN BUTTON
    $("#btnLogin").click(function (e) {
        e.preventDefault();

        let UserId = $("#UserId").val().trim();
        let password = $("#password").val().trim();
        let captchaEntered = $("#captchaInput").val().trim();
        let serverCaptcha = $("#serverCaptcha").val();

        if (!UserId || !password) {
            Swal.fire({
                icon: 'warning',
                title: 'Missing Information',
                text: 'Please enter both UserId and password.'
            });
            return;
        }

        if (captchaEntered.toLowerCase() !== serverCaptcha.toLowerCase()) {
            Swal.fire({
                icon: 'error',
                title: 'Invalid Captcha ❌',
                text: 'Captcha mismatch.'
            });

            refreshCaptcha();
            $("#captchaInput").val("");
            return;
        }

        showLoading(true);

        $.ajax({
            url: '@Url.Action("Login", "User")',
            type: 'POST',
            contentType: 'application/json',
            data: JSON.stringify({
                UserId: UserId,
                Password: password,
                Captcha: captchaEntered
            }),
            success: function (response) {
                showLoading(false);

                console.log("Login Response:", response);

                // ✅ OPEN OTP MODAL (BOOTSTRAP 5 SAFE)
                if (response.success && response.otpRequired) {
                    const otpModal = new bootstrap.Modal(document.getElementById('otpModal'));
                    otpModal.show();
                    return;
                }

                // ❌ LOGIN FAILED
                Swal.fire({
                    title: 'Login Failed!',
                    html: `<img src="${window.appRoot}AppImages/img14.gif" width="150">`,
                    showConfirmButton: false,
                    timer: 3000
                });

                refreshCaptcha();
                $("#captchaInput").val("");
                $("#password").val("");
            },
            error: function () {
                showLoading(false);
                Swal.fire({
                    icon: 'error',
                    title: 'Server Error ⚠️',
                    text: 'Unable to contact server.'
                });
            }
        });
    });

    // OTP VERIFY BUTTON
    $("#verifyOtpBtn").click(function () {

        let otp = $("#otpInput").val().trim();
        let userId = $("#UserId").val().trim();

        if (!otp) {
            Swal.fire("Please enter OTP");
            return;
        }

        showLoading(true);

        $.ajax({
            url: window.appRoot + "User/VerifyOtp",
            type: "POST",
            contentType: "application/json",
            data: JSON.stringify({
                UserId: userId,
                Otp: otp
            }),
            success: function (res) {
                showLoading(false);

                if (res.success) {
                    Swal.fire({
                        title: '🎉 Login Successful',
                        timer: 1500,
                        showConfirmButton: false
                    });

                    setTimeout(() => {
                        window.location.href = window.appRoot + "Face/GeoFencing";
                    }, 1200);
                } else {
                    Swal.fire({
                        icon: 'error',
                        title: 'OTP Failed',
                        text: res.message || 'Invalid OTP'
                    });
                }
            },
            error: function () {
                showLoading(false);
                Swal.fire("Server error while verifying OTP");
            }
        });
    });
</script>

<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>



public class RoadSideBarricadingEntryResult
{
    public string BarricadingRequestNo { get; set; }
    public DateTime BarricadingRequestOn { get; set; }
    public string BarricadingRequestBy { get; set; }

    public string Division { get; set; }
    public string Department { get; set; }

    public DateTime StartDate { get; set; }
    public DateTime EndDate { get; set; }

    public string GISApprovalNo { get; set; }

    public string SafetyApprovedBy { get; set; }
    public DateTime? SafetyApprovedOn { get; set; }

    public string SectionalInchargeApprovedBy { get; set; }
    public DateTime? SectionalInchargeApprovedOn { get; set; }

    public string? Coordinates { get; set; }

    public DateTime? RequestClosedOn { get; set; }
    public string RequestClosedBy { get; set; }
}


SELECT COUNT(*) 
FROM App_Road_side_barricade_Entry
WHERE SafetyHead_CreatedOn IS NULL
   OR SectionHead_CreatedOn IS NULL;

      
      
      
      public async Task<IEnumerable<RoadSideBarricadingEntryResult>>
   GetBarricadingEntriesAsync(DateTime fromDate, DateTime toDate)
      {
          using (var connection = new SqlConnection(_connectionString))
          {
              string sql = @"
      SELECT 
          Rs.ReqNo AS BarricadingRequestNo,
          Rs.CreatedOn AS BarricadingRequestOn,
          Rs.CreatedBy AS BarricadingRequestBy,

          Dv.Division AS Division,
          Dp.Department AS Department,

          Rs.Start_Date AS StartDate,
          Rs.End_Date AS EndDate,

          Rs.GISNo AS GISApprovalNo,

          Rs.SafetyHead_Pno AS SafetyApprovedBy,
          Rs.SafetyHead_CreatedOn AS SafetyApprovedOn,

          Rs.SectionHead_Pno AS SectionalInchargeApprovedBy,
          Rs.SectionHead_CreatedOn AS SectionalInchargeApprovedOn,

           NULLIF(Rs.Location_Coordinates,'') as Coordinates,

CASE 
      WHEN Rs.Status = 'Request Closed' 
      THEN Rs.SafetyHead_CreatedOn 
      ELSE NULL 
  END AS RequestClosedOn,

  CASE 
      WHEN Rs.Status = 'Request Closed' 
      THEN Rs.SafetyHead_Pno 
      ELSE NULL 
  END AS RequestClosedBy

      FROM App_Road_side_barricade_Entry RS
      INNER JOIN App_DivisionMaster Dv 
          ON Dv.ID = RS.Info_DivisionID
      INNER JOIN App_DepartmentMaster Dp 
          ON Dp.ID = RS.Info_DepartmentID
      WHERE 
          Rs.Start_Date >= @FromDate
          AND Rs.End_Date <= @ToDate
      ORDER BY Rs.Start_Date";

              return await connection.QueryAsync<RoadSideBarricadingEntryResult>(
                  sql,
                  new
                  {
                      FromDate = fromDate.Date,
                      ToDate = toDate.Date.AddDays(1).AddSeconds(-1)
                  });
          }
      }






BarricadingRequestNo	BarricadingRequestOn	BarricadingRequestBy	Division	Department	StartDate	EndDate	GISApprovalNo	SafetyApprovedBy	SafetyApprovedOn	SectionalInchargeApprovedBy	SectionalInchargeApprovedOn	Coordinates	RequestClosedOn	RequestClosedBy
12/26/0159	2025-12-15 16:29:56.850	151514	Jharkhand Business	PHHS & MSW, JB	2025-12-09 00:00:00.000	2025-12-18 00:00:00.000	WMD25A03966	NULL	NULL	NULL	NULL	NULL	NULL	NULL
12/26/0156	2025-12-15 11:20:09.843	151514	Jharkhand Business	JB SUPPORT SYSTEM, JB	2025-12-10 00:00:00.000	2025-12-26 00:00:00.000	PSD25A03855	NULL	NULL	NULL	NULL	NULL	NULL	NULL
12/26/0154	2025-12-12 11:54:25.827	151514	REAL ESTATE	CITY OFFICE, RE	2025-12-10 00:00:00.000	2025-12-22 00:00:00.000	WMD25A03966	NULL	NULL	NULL	NULL	NULL	NULL	NULL
12/26/0153	2025-12-12 11:22:38.627	151514	EB & UB	CHAKRADHARPUR WATER PROJECT	2025-12-11 00:00:00.000	2025-12-26 00:00:00.000	WMD25A03966	NULL	NULL	NULL	NULL	NULL	NULL	NULL
12/26/0151	2025-12-11 17:55:17.750	151514	EB & UB	GOBARGHATI  WATER PROJECT	2025-12-11 00:00:00.000	2025-12-26 00:00:00.000	PSD25A03855	NULL	NULL	NULL	NULL	NULL	NULL	NULL
12/26/0155	2025-12-12 15:15:36.807	151514	EB & UB	GOBARGHATI  WATER PROJECT	2025-12-11 00:00:00.000	2025-12-26 00:00:00.000	WMD25A03966	NULL	NULL	NULL	NULL	NULL	NULL	NULL
12/26/0157	2025-12-15 13:29:36.460	151514	ODISHA	SUKINDA O&M	2025-12-11 00:00:00.000	2025-12-22 00:00:00.000	WMD25A03966	NULL	NULL	NULL	NULL	NULL	NULL	NULL
12/26/0152	2025-12-11 17:58:40.343	151514	EB & UB	JSR WATER PROJECT	2025-12-11 00:00:00.000	2025-12-21 00:00:00.000	PSD25A03855	NULL	NULL	NULL	NULL	NULL	NULL	NULL
12/26/0158	2025-12-15 16:19:16.527	151514	Jharkhand Business	ICS, JB	2025-12-15 00:00:00.000	2025-12-13 00:00:00.000	PSD25A03855	NULL	NULL	NULL	NULL	NULL	NULL	NULL
12/26/0162	2025-12-16 16:24:52.123	151514	Jharkhand Business	JAMSHEDPUR TOWN SERVICES (TS), JB	2025-12-15 00:00:00.000	2025-12-17 00:00:00.000	WMD25A03966	NULL	NULL	NULL	NULL	NULL	NULL	NULL
12/26/0161	2025-12-16 11:28:56.160	151514	ODISHA	GOBARGHATI  WATER PROJECT	2025-12-15 00:00:00.000	2025-12-18 00:00:00.000	WMD25A03966	NULL	NULL	NULL	NULL	NULL	NULL	NULL
12/26/0160	2025-12-16 10:11:09.500	151514	Jharkhand Business	JAMSHEDPUR TOWN SERVICES (TS), JB	2025-12-16 00:00:00.000	2025-12-17 00:00:00.000	WMD25A03966	NULL	NULL	NULL	NULL	NULL	NULL	NULL
12/26/0163	2025-12-23 13:03:47.270	151514	EB & UB	JSR WATER PROJECT	2025-12-23 00:00:00.000	2025-12-26 00:00:00.000	PSD25A03855	NULL	NULL	NULL	NULL	NULL	NULL	NULL
12/26/0165	2025-12-23 17:09:31.850	151514	ODISHA	REHAB COLONY PHS, OB	2025-12-23 00:00:00.000	2025-12-27 00:00:00.000	WMD25A03966	NULL	NULL	NULL	NULL	NULL	NULL	NULL
12/26/0164	2025-12-23 16:16:56.727	151514	Jharkhand Business	JAMSHEDPUR TOWN SERVICES (TS), JB	2025-12-23 00:00:00.000	2025-12-26 00:00:00.000	PSD25A03855	NULL	NULL	NULL	NULL	NULL	NULL	NULL
12/26/0166	2025-12-24 13:43:32.867	151514	Jharkhand Business	JB SUPPORT SYSTEM, JB	2025-12-24 00:00:00.000	2025-12-31 00:00:00.000	test	NULL	NULL	NULL	NULL	22.79440275487151,86.19224128298949; 22.789674810878946,86.19406518511965; 22.790644151667966,86.18850764804066; 22.79440275487151,86.19224128298949	NULL	NULL
12/26/0167	2025-12-26 16:50:40.747	151514	Jharkhand Business	IC O&M - TOWN CONSTRUCTION (TC), JB	2025-12-25 00:00:00.000	2025-12-31 00:00:00.000	Information Technology25A03783	NULL	NULL	NULL	NULL	22.81402493698422,86.16162118487547; 22.815745699223292,86.1732083278201; 22.813589798261617,86.17713508181802; 22.812462386922945,86.17522534899936; 22.8122448152396,86.17282208972195; 22.81155253938972,86.16730746798719; 22.812145918904996,86.16689977221692; 22.81256128302777,86.16449651293951; 22.81238326998738,86.16183576159665; 22.81402493698422,86.16162118487547	NULL	NULL
