.issue-box {
    background: linear-gradient(145deg, #ffffff, #f4f7fb);
    border: 1px solid #e2e6eb;
    border-radius: 10px;
    padding: 14px 18px;
    margin: 20px auto;
    max-width: 380px;
    text-align: center;
    color: #444;
    font-size: 15px;
    box-shadow: 0 3px 8px rgba(0, 0, 0, 0.06);
    transition: transform 0.25s ease, box-shadow 0.25s ease;
}

.issue-box:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 12px rgba(0, 0, 0, 0.1);
}

.issue-box i {
    font-size: 20px;
    color: #007bff;
    margin-bottom: 8px;
}

.issue-box p {
    margin: 0;
    line-height: 1.5;
}

.issue-link {
    display: inline-block;
    margin-top: 5px;
    color: #007bff;
    font-weight: 600;
    text-decoration: none;
    transition: all 0.3s ease;
}

.issue-link:hover {
    color: #0056b3;
    text-decoration: underline;
}

@media (max-width: 480px) {
    .issue-box {
        padding: 12px 14px;
        font-size: 14px;
        max-width: 90%;
    }

    .issue-box i {
        font-size: 18px;
    }
}

<div class="issue-box text-center mt-4">
    <p>
        <i class="fa-solid fa-circle-info me-2"></i><br>
        Having trouble?<br>
        <a href="/TSUISLARS/Geo/GeoFencing" class="issue-link">Click here for previous version</a>
    </p>
</div>




this is my viewside 

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

         <div class="issue-box text-center mt-4">
            <p>
                <i class="fa-solid fa-circle-info me-2" style="font-size: 18px;"></i>

                Having trouble?<br>
                <a href="/TSUISLARS/Geo/GeoFencing" class="issue-link">Click here for previous version</a>
            </p>
        </div>

    </div>
</form>


and this is my css

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


please make my info box makes good ui or best option to look on Mobile
