/* Bracket container covers 100% but corners shift slightly outside */
.face-brackets {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    pointer-events: none;
}

/* Corner size based on circle width */
.corner {
    position: absolute;

    width: 18%;       /* responsive */
    height: 18%;      /* responsive */

    border: 3px solid #1e90ff;
    border-radius: 8px;

    animation: cornerGlow 2s infinite ease-in-out;
}

/* ----- Corrected Edge Alignment (Outward Shift) ----- */

/* TOP-LEFT */
.corner.tl {
    top: 0;
    left: 0;
    transform: translate(-15%, -15%);
    border-right: none;
    border-bottom: none;
}

/* TOP-RIGHT */
.corner.tr {
    top: 0;
    right: 0;
    transform: translate(15%, -15%);
    border-left: none;
    border-bottom: none;
}

/* BOTTOM-LEFT */
.corner.bl {
    bottom: 0;
    left: 0;
    transform: translate(-15%, 15%);
    border-right: none;
    border-top: none;
}

/* BOTTOM-RIGHT */
.corner.br {
    bottom: 0;
    right: 0;
    transform: translate(15%, 15%);
    border-left: none;
    border-top: none;
}





video {
    transform: scaleX(-1);
}

/* ------- Responsive Camera Container (Face ID Circle) ------- */
#videoContainer {
    width: 80vw;             /* responsive width */
    height: 80vw;            /* perfect square */
    max-width: 260px;        /* limits on large screens */
    max-height: 260px;
    
    border-radius: 50%;
    overflow: hidden;

    border: 5px solid transparent;
    transition: border-color 0.3s ease;

    display: flex;
    justify-content: center;
    align-items: center;

    background: #f5f5f5;
    margin: 0 auto;
    position: relative;
}

/* Camera video and captured image scale responsively */
#video, #capturedImage {
    width: 100% !important;
    height: 100% !important;
    object-fit: cover;
    border-radius: 50%;
}

/* ------- Brackets Container - Responsive Positioning ------- */
.face-brackets {
    position: absolute;
    width: 88%;
    height: 88%;
    top: 6%;
    left: 6%;
    pointer-events: none;
}

/* ------- Corner Brackets (Responsive) ------- */
.corner {
    position: absolute;

    /* Responsive size */
    width: 16%;
    height: 16%;

    border: 3px solid #1e90ff;
    border-radius: 8px;

    animation: cornerGlow 2s ease-in-out infinite;
    opacity: 1;
}

/* Individual corner cut patterns */
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

/* Glow animation */
@keyframes cornerGlow {
    0% { opacity: 0.5; transform: scale(1); }
    50% { opacity: 1; transform: scale(1.15); }
    100% { opacity: 0.5; transform: scale(1); }
}

/* ------- Color States (Scan / Error / Success) ------- */
#videoContainer.success .corner {
    border-color: #00c853;
    box-shadow: 0 0 14px rgba(0, 200, 83, 0.5);
}

#videoContainer.error .corner {
    border-color: #ff1744;
    box-shadow: 0 0 14px rgba(255, 23, 68, 0.5);
}

#videoContainer.scanning .corner {
    border-color: #1e90ff;
}

/* ------- Status Text Responsive ------- */
#statusText {
    font-weight: bold;
    margin-top: 20px;
    font-size: 4vw;  /* responsive */
    max-font-size: 16px;
    color: #444;
    text-align: center;
}

@media (min-width: 400px) {
    #statusText {
        font-size: 16px;
    }
}





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
