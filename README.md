<script>
    let isLoginProcessing = false;

    function showLoading(show) {
        if (show)
            $("#loading-overlay").css("display", "flex");
        else
            $("#loading-overlay").hide();
    }

    /* ==============================
       ENTER KEY HANDLING (FIXED)
    ============================== */
    $(document).on("keydown", function (e) {
        if (e.key === "Enter") {
            e.preventDefault();

            // If OTP modal open → verify OTP
            if ($("#otpModal").hasClass("show")) {
                $("#verifyOtpBtn").click();
            }
            // Else → login
            else {
                $("#btnLogin").click();
            }
        }
    });

    /* ==============================
       CAPTCHA
    ============================== */
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

    document.addEventListener("DOMContentLoaded", function () {
        styleCaptcha($("#serverCaptcha").val());
    });

    function refreshCaptcha() {
        fetch(window.appRoot + 'User/RefreshCaptcha')
            .then(res => res.json())
            .then(data => {
                $("#serverCaptcha").val(data.captcha);
                styleCaptcha(data.captcha);
            });
    }

    /* ==============================
       LOGIN
    ============================== */
    $("#btnLogin").click(function (e) {
        e.preventDefault();

        if (isLoginProcessing) return;
        isLoginProcessing = true;

        let UserId = $("#UserId").val().trim();
        let password = $("#password").val().trim();
        let captchaEntered = $("#captchaInput").val().trim();
        let serverCaptcha = $("#serverCaptcha").val();

        if (!UserId || !password) {
            Swal.fire("Please enter UserId and Password");
            isLoginProcessing = false;
            return;
        }

        if (captchaEntered.toLowerCase() !== serverCaptcha.toLowerCase()) {
            Swal.fire("Invalid Captcha ❌");
            refreshCaptcha();
            $("#captchaInput").val("");
            isLoginProcessing = false;
            return;
        }

        $("#btnLogin").prop("disabled", true);
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
                isLoginProcessing = false;
                $("#btnLogin").prop("disabled", false);

                if (response.success && response.otpRequired) {

                    const otpModal = new bootstrap.Modal(
                        document.getElementById('otpModal'),
                        {
                            backdrop: 'static', // ❌ outside click disabled
                            keyboard: false     // ❌ ESC disabled
                        }
                    );

                    $("#otpInput").val("");
                    otpModal.show();
                    return;
                }

                Swal.fire({
                    title: response.message || "Incorrect Password",
                    icon: "error",
                    timer: 3000,
                    showConfirmButton: false
                });

                refreshCaptcha();
                $("#password").val("");
            },
            error: function () {
                showLoading(false);
                isLoginProcessing = false;
                $("#btnLogin").prop("disabled", false);

                Swal.fire("Server Error ⚠️");
            }
        });
    });

    /* ==============================
       OTP VERIFY
    ============================== */
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
                        title: '🎉 Welcome Back!',
                        timer: 2000,
                        showConfirmButton: false
                    });

                    setTimeout(() => {
                        window.location.href = window.appRoot + "Face/GeoFencing";
                    }, 1000);
                } else {
                    Swal.fire(res.message || "Invalid or Expired OTP");
                    $("#otpInput").val("");
                }
            },
            error: function () {
                showLoading(false);
                Swal.fire("OTP verification failed");
            }
        });
    });

    /* ==============================
       MANUAL CLOSE BUTTON
    ============================== */
    function hideOtpModal() {
        const modalEl = document.getElementById('otpModal');
        const modal = bootstrap.Modal.getInstance(modalEl);
        if (modal) modal.hide();
    }
</script>




<script>
    function showLoading(show) {
        if (show)
            $("#loading-overlay").css("display", "flex");
        else
            $("#loading-overlay").hide();
    }

    $(document).on("keypress", function (e) {
        if (e.which === 13) {
            $("#btnLogin").click();
        }
    });

    $(document).on("keydown", function (e) {
    if (e.key === "Enter") {
        e.preventDefault();

       
        if ($("#otpModal").hasClass("show")) {
            $("#verifyOtpBtn").click();
        }
        
        else {
            $("#btnLogin").click();
        }
    }
});


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


    document.addEventListener("DOMContentLoaded", function () {
        styleCaptcha($("#serverCaptcha").val());
    });


    function refreshCaptcha() {
        fetch(window.appRoot + 'User/RefreshCaptcha')
            .then(res => res.json())
            .then(data => {
                $("#serverCaptcha").val(data.captcha);
                styleCaptcha(data.captcha);
            });
    }


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

        
                if (response.success && response.otpRequired) {
                    const otpModal = new bootstrap.Modal(document.getElementById('otpModal'));
                    otpModal.show();
                    return;
                }

          
                Swal.fire({
                        title: 'Incorrect Password',
                         html: `<img src="${window.appRoot}AppImages/img14.gif" width="150">`,
                        showConfirmButton: false,
                        timer: 3000,
                        background: '#f4f6f9',
                        backdrop: `
                            rgb(121 0 0 / 40%)                       
                            left top
                            no-repeat
                        `
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
                    title: '🎉 Welcome Back!',
                    html: `<img src="${window.appRoot}AppImages/img9.jpg" width="150">`,
                    showConfirmButton: false,
                    timer: 2500,
                    background: '#f4f6f9',
                    backdrop: `
                            rgba(0,0,123,0.4)
                            url("/images/party.gif")
                            left top
                            no-repeat
                        `
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

                     $("#otpInput").val("");
                }
            },
            error: function () {
                showLoading(false);
                Swal.fire("Server error while verifying OTP");
            }
        });
    });

     function hideOtpModal() {
    const modalEl = document.getElementById('otpModal');
    const modal = bootstrap.Modal.getInstance(modalEl);
    if (modal) {
        modal.hide();
    }
}

</script>
