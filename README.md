string htmlBody = @"
<!DOCTYPE html>
<html>
<head>
<meta charset='utf-8'>
</head>

<body style='margin:0; padding:0; background:#dce6f2; font-family:Arial, sans-serif;'>

<!-- MAIN WRAPPER -->
<table width='100%' cellpadding='0' cellspacing='0' style='background:#dce6f2; padding:20px 0;'>
<tr>
<td align='center'>

    <!-- CONTENT CARD -->
    <table width='700' cellpadding='0' cellspacing='0' style='background:#ffffff; border:1px solid #b8c6d0;'>

        <!-- HEADER -->
        <tr>
            <td style='background:#e6eef6; padding:15px 20px; font-size:18px; font-weight:bold; color:#000; border-bottom:1px solid #c0ccd6;'>
                Holiday Home Booking Confirmed ({REF_NO})
            </td>
        </tr>

        <!-- CREATED ROW -->
        <tr>
            <td style='padding:15px 20px; background:#f5f9fd; border-bottom:1px solid #cfd8e2;'>
                <table cellpadding='0' cellspacing='0'>
                    <tr>
                        <td style='vertical-align:middle;'>
                            <img src='https://cdn-icons-png.flaticon.com/512/2088/2088617.png' width='20' height='20' />
                        </td>
                        <td style='padding-left:10px; font-size:14px; color:#000;'>
                            Created &nbsp;&nbsp; <strong>{CARD_ID}</strong>
                        </td>
                    </tr>
                </table>
            </td>
        </tr>

        <!-- MESSAGE BODY -->
        <tr>
            <td style='padding:25px 20px; background:#e6eef6; font-size:15px; color:#000;'>

                <p>Dear <strong>{EMPLOYEE_NAME} ({EMP_ID})</strong>,</p>

                <p>Your application for Holiday Home booking at <strong>{HOTEL_NAME}</strong>, {LOCATION} 
                from <strong>{FROM_DATE}</strong> to <strong>{TO_DATE}</strong> is confirmed.</p>

                <p>Please carry a printout of the booking permit along with you during your visit.</p>

                <p>Kindly comply with the Terms &amp; Conditions given in the permit during your stay.</p>

                <p>Have a pleasant stay ahead!!</p>

                <p>With warm regards,<br/>
                Area Manager,<br/>
                Employment Bureau</p>

            </td>
        </tr>

        <!-- FOOTER LINK -->
        <tr>
            <td style='padding:15px 20px; background:#ffffff; border-top:1px solid #cfd8e2;'>
                <a href='{PERMIT_LINK}' style='font-size:15px; color:#003399; text-decoration:underline;'>
                    Holiday Home Permit
                </a>
            </td>
        </tr>

    </table>

</td>
</tr>
</table>

</body>
</html>";





html structure:

<div id="timerScreen" class="timer-container" style="display:none;">
    <div class="timer-card">
        <div class="loader"></div>

        <h2>Please wait...</h2>
        <p>You can punch again after</p>

        <h1 id="countdownTimer" class="countdown-display">00:00</h1>
    </div>
</div>


css:

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
    from {
        opacity: 0;
    }

    to {
        opacity: 1;
    }
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
    from {
        transform: translateY(40px);
        opacity: 0;
    }

    to {
        transform: translateY(0);
        opacity: 1;
    }
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
        -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
        -webkit-mask-composite: xor;
        mask-composite: exclude;
        animation: rotateBorder 3s linear infinite;
    }

@keyframes rotateBorder {
    from {
        transform: rotate(0);
    }

    to {
        transform: rotate(360deg);
    }
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
    to {
        transform: rotate(360deg);
    }
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

js:



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
