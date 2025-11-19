<!-- HTML (unchanged structure, just classes) -->
<div id="timerScreen" class="timer-container hidden" aria-hidden="true">
  <div class="timer-card" role="dialog" aria-label="Punch timer">
    <div class="loader" aria-hidden="true"></div>
    <h2>Please wait...</h2>
    <p>You can punch again after</p>
    <h1 id="countdownTimer" class="countdown-display">00:00</h1>
  </div>
</div>

/* CSS - Centering WITHOUT forcing display */
/* The overlay is fixed and centered using transform; no flex needed. */
.timer-container {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);

  /* Use auto sizing so no display override is necessary */
  width: 90%;
  max-width: 380px;
  padding: 18px;
  z-index: 99999;

  /* backdrop + glass */
  background: rgba(255,255,255,0.06);
  border: 1px solid rgba(255,255,255,0.08);
  border-radius: 16px;
  backdrop-filter: blur(10px) saturate(1.1);

  /* smooth entrance */
  transition: opacity 260ms ease, transform 260ms ease, visibility 260ms;
  box-shadow: 0 10px 30px rgba(0,0,0,0.45);
  text-align: center;
  color: #fff;
  will-change: opacity, transform;
}

/* Hidden/Visible states (no display changes) */
.timer-container.hidden {
  opacity: 0;
  visibility: hidden;
  pointer-events: none;
  transform: translate(-50%, -48%); /* slight offset while hidden */
}

.timer-container.visible {
  opacity: 1;
  visibility: visible;
  pointer-events: auto;
  transform: translate(-50%, -50%);
}

/* Inner card for layout */
.timer-card {
  padding: 24px 20px;
  border-radius: 12px;
  position: relative;
}

/* Countdown UI */
.countdown-display {
  font-size: 54px;
  font-weight: 700;
  margin: 10px 0 0;
  letter-spacing: 1px;
}

/* small loader */
.loader {
  width: 56px;
  height: 56px;
  margin: 0 auto 12px;
  border-radius: 50%;
  border: 5px solid rgba(255,255,255,0.18);
  border-top-color: rgba(255,255,255,0.95);
  animation: spin 0.95s linear infinite;
}

/* subtle spin */
@keyframes spin { to { transform: rotate(360deg); } }

/* OPTIONAL: responsive tweak */
@media (max-width: 420px) {
  .countdown-display { font-size: 42px; }
  .timer-container { max-width: 320px; padding: 16px; }
}

/* JS: toggle classes instead of style.display */
function showMainUI() {
  // hide timer overlay
  const timer = document.getElementById("timerScreen");
  timer.classList.remove("visible");
  timer.classList.add("hidden");
  timer.setAttribute("aria-hidden", "true");

  // restore main UI - use your existing show logic, e.g.:
  const main = document.getElementById("mainFormContainer");
  if (main) {
    // if your global CSS hides main with display styles, keep using that:
    main.style.display = "block";
  }
}

function showTimerUI() {
  const timer = document.getElementById("timerScreen");

  // make sure main UI is hidden via display (so we don't conflict)
  const main = document.getElementById("mainFormContainer");
  if (main) main.style.display = "none";

  // show overlay using classes (no display changes)
  timer.classList.remove("hidden");
  timer.classList.add("visible");
  timer.setAttribute("aria-hidden", "false");
}

/* update startCountdown to call showTimerUI() */
function startCountdown(unlockTime) {
  // Use class toggle to show overlay
  showTimerUI();

  const timerLabel = document.getElementById("countdownTimer");

  // clear any existing interval stored on the element
  if (window._punchCountdownInterval) {
    clearInterval(window._punchCountdownInterval);
    window._punchCountdownInterval = null;
  }

  window._punchCountdownInterval = setInterval(() => {
    const now = new Date();
    const diff = unlockTime - now;

    if (diff <= 0) {
      clearInterval(window._punchCountdownInterval);
      window._punchCountdownInterval = null;

      // hide timer and show main UI
      showMainUI();
      OnOff(); // your existing function
      return;
    }

    const minutes = Math.floor(diff / 60000);
    const seconds = Math.floor((diff % 60000) / 1000);

    timerLabel.textContent =
      `${minutes.toString().padStart(2, "0")}:${seconds.toString().padStart(2, "0")}`;
  }, 250); // 250ms tick to keep smooth numbers when going from mm:ss -> 00:xx
}





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
