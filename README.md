<div class="row g-2">
    <div class="col-md-4">
        <label for="KPISPOC">KPI SPOC (PNO)</label>
        <input asp-for="KPISPOC" class="form-control form-control-sm" id="KPISPOC" autocomplete="off" placeholder="Enter PNO">
    </div>

    <div class="col-md-4">
        <label for="ImmediateSuperior">Immediate Superior</label>
        <input asp-for="ImmediateSuperior" class="form-control form-control-sm" id="ImmediateSuperior" autocomplete="off" readonly>
    </div>
</div>

@section Scripts {
<script>
    $(document).ready(function () {
        let typingTimer;
        const typingDelay = 500; // milliseconds (wait 0.5s after typing)

        $("#KPISPOC").on("input", function () {
            clearTimeout(typingTimer);
            const pno = $(this).val().trim();

            if (pno === "") {
                $("#ImmediateSuperior").val("");
                return;
            }

            typingTimer = setTimeout(function () {
                $.ajax({
                    url: '/KPI/GetImmediateSuperior',
                    type: 'GET',
                    data: { pno: pno },
                    success: function (response) {
                        if (response.success && response.data) {
                            $("#ImmediateSuperior").val(response.data);
                        } else {
                            $("#ImmediateSuperior").val("");
                        }
                    },
                    error: function () {
                        console.error("Error fetching Immediate Superior");
                    }
                });
            }, typingDelay);
        });
    });
</script>
}




using Microsoft.AspNetCore.Mvc;
using Dapper;
using System.Data;
using System.Data.SqlClient;

public class KPIController : Controller
{
    private readonly IConfiguration _configuration;

    public KPIController(IConfiguration configuration)
    {
        _configuration = configuration;
    }

    [HttpGet]
    public async Task<IActionResult> GetImmediateSuperior(string pno)
    {
        if (string.IsNullOrEmpty(pno))
            return Json(new { success = false, message = "PNO is required" });

        string connectionString = _configuration.GetConnectionString("DefaultConnection");

        using (var connection = new SqlConnection(connectionString))
        {
            await connection.OpenAsync();

            string sql = @"SELECT ema_reporting_to_pno 
                           FROM SAPHRDB.dbo.T_Empl_All 
                           WHERE ema_perno = @pno";

            var result = await connection.QueryFirstOrDefaultAsync<string>(sql, new { pno });

            if (result != null)
                return Json(new { success = true, data = result });
            else
                return Json(new { success = false, message = "No data found" });
        }
    }
}

<div class="row g-2">
    <div class="col-md-4">
        <label for="KPISPOC">KPI SPOC (PNO)</label>
        <input asp-for="KPISPOC" class="form-control form-control-sm" id="KPISPOC" autocomplete="off" placeholder="Enter PNO">
    </div>

    <div class="col-md-4">
        <label for="ImmediateSuperior">Immediate Superior</label>
        <input asp-for="ImmediateSuperior" class="form-control form-control-sm" id="ImmediateSuperior" autocomplete="off" readonly>
    </div>
</div>

@section Scripts {
<script>
    $(document).ready(function () {
        $("#KPISPOC").on("change", function () {
            var pno = $(this).val().trim();

            if (pno === "") {
                $("#ImmediateSuperior").val("");
                return;
            }

            $.ajax({
                url: '/KPI/GetImmediateSuperior',
                type: 'GET',
                data: { pno: pno },
                success: function (response) {
                    if (response.success && response.data) {
                        $("#ImmediateSuperior").val(response.data);
                    } else {
                        $("#ImmediateSuperior").val("");
                        alert(response.message || "No record found");
                    }
                },
                error: function () {
                    alert("Error connecting to server");
                }
            });
        });
    });
</script>
}





this is my query 

select ema_reporting_to_pno from SAPHRDB.dbo.T_Empl_All where ema_perno ='151514'

this is my two inputs 
 
<input asp-for="KPISPOC" class="form-control form-control-sm" id="KPISPOC" autocomplete="off">

 <input asp-for="ImmediateSuperior" class="form-control form-control-sm" id="ImmediateSuperior" autocomplete="off">

i want when user input the Pno in KPI Spoc then automatically show Immediate Superior
