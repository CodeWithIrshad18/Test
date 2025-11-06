<form asp-action="AttendanceData" id="form" asp-controller="Geo" method="post">
    <div class="text-center camera">
        <div id="videoContainer" style="display: inline-block;width: 195px; border: 4px solid transparent; border-radius: 8px; transition: border-color 0.3s ease;">
            <video id="video" width="185" height="240" autoplay muted playsinline></video>
            <img id="capturedImage" style="display:none; width: 186px; height: 240px; border-radius: 8px;" />
        </div>
        <canvas id="canvas" style="display:none;"></canvas>
        <p id="statusText" style="font-weight: bold; margin-top: 10px; color: #444;"></p>
    </div>

    <input type="hidden" name="Type" id="EntryType" />
    <input type="hidden" id="Entry" value="@((ViewBag.InOut == "I") ? "Punch In" : "Punch Out")" />

    <div class="mt-5 form-group">
        <div class="col d-flex justify-content-center mb-4">
            @if (ViewBag.InOut == "I")
            {
                <button type="button" class="Btn" id="PunchIn" onclick="captureImageAndSubmit('Punch In')">Punch In</button>
            }
        </div>
        <div class="col d-flex justify-content-center">
            @if (ViewBag.InOut == "O")
            {
                <button type="button" class="Btn2" id="PunchOut" onclick="captureImageAndSubmit('Punch Out')">Punch Out</button>
            }
        </div>

        <!-- Support Message Box -->
        <div class="issue-box text-center mt-4">
            <p>
                Having trouble with face detection or camera access? <br>
                <a href="/Geo/FaceSupport" class="issue-link">Click here for help</a>
            </p>
        </div>
    </div>
</form>

<style>
    .issue-box {
        background: #f9fafc;
        border: 1px solid #e0e0e0;
        border-radius: 8px;
        padding: 10px 15px;
        max-width: 360px;
        margin: 0 auto;
        font-size: 14px;
        color: #555;
        box-shadow: 0 2px 5px rgba(0, 0, 0, 0.05);
        transition: all 0.3s ease;
    }

    .issue-box:hover {
        background: #f1f4f8;
        border-color: #d0d0d0;
    }

    .issue-link {
        color: #0066cc;
        font-weight: 600;
        text-decoration: none;
        transition: color 0.3s ease;
    }

    .issue-link:hover {
        color: #004999;
        text-decoration: underline;
    }
</style>






[HttpGet]

public async Task<JsonResult> GetTargets(Guid TSID)
{
    using (var connection = new SqlConnection(GetSAPConnectionString()))
    {
        string query = @"
            SELECT 
                pm.ID,
                tj.PeriodicityTransactionID,
                tj.TargetValue,
                ISNULL(t2.Value, NULL) AS Value,
                ISNULL(t2.YTDValue, NULL) AS YTDValue,
                t2.Hold,
                t2.HoldReason
            FROM App_KPIMaster_NOPR ts
            INNER JOIN App_TargetSetting_NOPR td
                ON ts.ID = td.KPIID
            INNER JOIN App_TargetSettingDetails_NOPR tj
                ON td.ID = tj.MasterID
            INNER JOIN App_PeriodicityTransaction pm
                ON pm.PeriodicityName = tj.PeriodicityTransactionID
            OUTER APPLY (
                SELECT TOP 1 
                    t.Value, 
                    t.YTDValue, 
                    t.Hold, 
                    t.HoldReason
                FROM App_KPIDetails_NOPR t
                WHERE t.KPIID = ts.ID 
                  AND t.PeriodTransactionID = pm.ID
                ORDER BY t.CreatedOn DESC
            ) t2
            WHERE td.ID = @TSID
            ORDER BY pm.Sl_no;
        ";

        var result = await connection.QueryAsync(query, new { TSID });
        return Json(result);
    }
}

<form asp-action="AttendanceData" id="form" asp-controller="Geo" method="post">
    <div class="text-center camera">
        <div id="videoContainer" style="display: inline-block;width: 195px; border: 4px solid transparent; border-radius: 8px; transition: border-color 0.3s ease;">
            <video id="video" width="185" height="240" autoplay muted playsinline></video>
            <img id="capturedImage" style="display:none; width: 186px; height: 240px; border-radius: 8px;" />
        </div>
        <canvas id="canvas" style="display:none;"></canvas>
        <p id="statusText" style="font-weight: bold; margin-top: 10px; color: #444;"></p>
    </div>

    

    <input type="hidden" name="Type" id="EntryType" />
    <input type="hidden" id="Entry" value="@((ViewBag.InOut == "I") ? "Punch In" : "Punch Out")" />

    <div class="mt-5 form-group">
        <div class="col d-flex justify-content-center mb-4">
            @if (ViewBag.InOut == "I")
            {
                <button type="button" class="Btn" id="PunchIn" onclick="captureImageAndSubmit('Punch In')">Punch In</button>
            }
        </div>
        <div class="col d-flex justify-content-center">
            @if (ViewBag.InOut == "O")
            {
                <button type="button" class="Btn2" id="PunchOut" onclick="captureImageAndSubmit('Punch Out')">Punch Out</button>
            }
        </div>
    </div>
</form>
