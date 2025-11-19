<div id="timerScreen" class="timer-container" style="display:none;">
    <div class="timer-card">
        <div class="loader"></div>

        <h2>Please wait...</h2>
        <p>You can punch again after</p>

        <h1 id="countdownTimer" class="countdown-display">00:00</h1>
    </div>
</div>

/* Fullscreen Center Container */
.timer-container {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    backdrop-filter: blur(8px);
    background: rgba(0,0,0,0.35);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 9999;
    animation: fadeIn 0.4s ease;
}

/* Smooth fade animation */
@keyframes fadeIn {
    from { opacity: 0; }
    to { opacity: 1; }
}

/* Glass effect card */
.timer-card {
    padding: 40px 50px;
    background: rgba(255,255,255,0.15);
    border-radius: 20px;
    backdrop-filter: blur(12px);
    border: 1px solid rgba(255,255,255,0.25);
    text-align: center;
    width: 85%;
    max-width: 350px;
    color: #fff;
    animation: slideUp 0.5s ease;
}

/* Slide animation */
@keyframes slideUp {
    from { transform: translateY(40px); opacity: 0; }
    to { transform: translateY(0); opacity: 1; }
}

/* Gradient animated border */
.timer-card {
    position: relative;
}

.timer-card::before {
    content: "";
    position: absolute;
    inset: 0;
    border-radius: 20px;
    padding: 2px;
    background: linear-gradient(120deg, #ff8a00, #e52e71, #4b69ff);
    -webkit-mask: 
        linear-gradient(#fff 0 0) content-box, 
        linear-gradient(#fff 0 0);
    -webkit-mask-composite: xor;
            mask-composite: exclude;
    animation: rotateBorder 3s linear infinite;
}

@keyframes rotateBorder {
    from { transform: rotate(0); }
    to { transform: rotate(360deg); }
}

/* Countdown timer style */
.countdown-display {
    font-size: 58px;
    font-weight: bold;
    margin-top: 10px;
    letter-spacing: 2px;
}

/* Loading pulse circle */
.loader {
    width: 60px;
    height: 60px;
    margin: auto;
    border-radius: 50%;
    border: 5px solid #ffffff50;
    border-top-color: #ffffff;
    animation: spin 1s linear infinite;
    margin-bottom: 20px;
}

@keyframes spin {
    to { transform: rotate(360deg); }
}

h2 {
    margin-bottom: 5px;
    font-weight: 600;
}

p {
    margin: 0;
    margin-bottom: 10px;
    opacity: 0.8;
}




<div id="timerScreen" style="display:none; text-align:center; margin-top:40px;">
    <h2>Please wait...</h2>
    <p>You can punch again after.</p>
    <h1 id="countdownTimer" style="font-size:48px; font-weight:bold;"></h1>
    
</div>


<script>
document.addEventListener("DOMContentLoaded", function () {

    const lastPunch = "@ViewBag.LatestPunchTime";  

    if (!lastPunch || lastPunch.trim() === "") {
        showMainUI();
        OnOff();
        return;
    }

    const today = new Date();
    const [hh, mm] = lastPunch.split(":").map(Number);

    const lastPunchDateTime = new Date(
        today.getFullYear(),
        today.getMonth(),
        today.getDate(),
        hh,
        mm,
        0
    );

    const unlockTime = new Date(lastPunchDateTime.getTime() + 2 * 60000);
    const now = new Date();

    if (now < unlockTime) {
        startCountdown(unlockTime);
    } else {
        showMainUI();
        OnOff();
    }

});

function startCountdown(unlockTime) {

    document.getElementById("mainFormContainer").style.display = "none";

    document.getElementById("timerScreen").style.display = "block";

    const timerLabel = document.getElementById("countdownTimer");

    const interval = setInterval(() => {
        const now = new Date();
        const diff = unlockTime - now;

        if (diff <= 0) {
            clearInterval(interval);

            showMainUI();

            OnOff();

            return;
        }

        const minutes = Math.floor(diff / 60000);
        const seconds = Math.floor((diff % 60000) / 1000);

        timerLabel.textContent =
            `${minutes.toString().padStart(2, "0")}:${seconds
                .toString().padStart(2, "0")}`;

    }, 1000);
}

function showMainUI() {
    document.getElementById("timerScreen").style.display = "none";
    document.getElementById("mainFormContainer").style.display = "block";
}
</script>
