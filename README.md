make it responsive 

<style>

    video {
        transform: scaleX(-1);
    }


    #videoContainer {
        width: 220px;
        height: 220px;
        border-radius: 50%;
        overflow: hidden;
        border: 6px solid transparent;
        transition: border-color 0.3s ease;
        display: flex;
        justify-content: center;
        align-items: center;
        background: #f5f5f5;
        margin: auto;
    }


    #video, #capturedImage {
        width: 240px !important;
        height: 240px !important;
        object-fit: cover;
        border-radius: 50%;
      
    }

.face-brackets {
    position: absolute;
    width: 70%;
    height: 33%;
    top: 12%;
    left: 16%;
    pointer-events: none;
}

.corner {
    position: absolute;
    width: 40px;
    height: 40px;
    border: 4px solid #1e90ff;
    border-radius: 10px;
    opacity: 1;
    animation: cornerGlow 2s ease-in-out infinite;
}

.corner.tl {
    top: 0;
    left: 0;
    border-right: none;
    border-bottom: none;
}


.corner.tr {
    top: 0;
    right: 0;
    border-left: none;
    border-bottom: none;
}

.corner.bl {
    bottom: 0;
    left: 0;
    border-right: none;
    border-top: none;
}

.corner.br {
    bottom: 0;
    right: 0;
    border-left: none;
    border-top: none;
}


#videoContainer.success .corner {
    border-color: #00c853;
    box-shadow: 0 0 18px rgba(0, 200, 83, 0.5);
}

#videoContainer.error .corner {
    border-color: #ff1744;
    box-shadow: 0 0 18px rgba(255, 23, 68, 0.5);
}

#videoContainer.scanning .corner {
    border-color: #1e90ff;
}


    #statusText {
        font-weight: bold;
        margin-top: 80px;
        font-size: 14px;
        color: #444;
    }
</style>

<form asp-action="AttendanceData" id="form" asp-controller="Geo" method="post">
    <div class="text-center camera">
      <div id="videoContainer" class="scanning">
    <div class="face-brackets mb-3">
        <span class="corner tl"></span>
        <span class="corner tr"></span>
        <span class="corner bl"></span>
        <span class="corner br"></span>
    </div>

    <video id="video" autoplay muted playsinline></video>
    <img id="capturedImage" style="display:none;" />
</div>


        <canvas id="canvas" style="display:none;"></canvas>
        <p id="statusText" style="font-weight: bold; margin-top: 10px; color: #444;"></p>
    </div>

    

    <input type="hidden" name="Type" id="EntryType" />
    <input type="hidden" id="Entry" value="@((ViewBag.InOut == "I") ? "Punch In" : "Punch Out")" />

    <div class="mt-3 form-group">
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

         <div class="issue-box text-center mt-3">
    <p>
        <i class="fa-solid fa-circle-info me-2"></i>
        Having trouble?<br>
        <a href="/TSUISLARS/Geo/GeoFencing" class="issue-link">Click here for previous version</a>
    </p>
</div>

    </div>
</form>
