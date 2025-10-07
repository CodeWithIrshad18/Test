<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Login</title>

    <!-- ✅ Bootstrap 5 -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">

    <!-- ✅ SweetAlert2 -->
    <script src="https://cdn.jsdelivr.net/npm/sweetalert2@11"></script>

    <style>
        body {
            background: linear-gradient(135deg, #0078d7, #00a2ff);
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            font-family: "Segoe UI", sans-serif;
        }

        .login-card {
            background: #fff;
            padding: 40px 30px;
            border-radius: 10px;
            box-shadow: 0 5px 20px rgba(0,0,0,0.1);
            width: 100%;
            max-width: 350px;
        }

        .form-control {
            margin-bottom: 15px;
        }

        #loading-overlay {
            display: none;
            position: fixed;
            top: 0; left: 0;
            width: 100%; height: 100%;
            background: rgba(255, 255, 255, 0.8);
            z-index: 1055;
            justify-content: center;
            align-items: center;
        }

        .spinner-border {
            width: 3rem;
            height: 3rem;
        }
    </style>
</head>

<body>
    <!-- Login Form -->
    <div class="login-card text-center">
        <h3 class="mb-4">User Login</h3>
        <input type="text" class="form-control" id="ADID" placeholder="Enter ADID">
        <input type="password" class="form-control" id="password" placeholder="Enter Password">
        <button type="button" id="btnLogin" class="btn btn-primary w-100">Login</button>
    </div>

    <!-- Loading Overlay -->
    <div id="loading-overlay">
        <div class="spinner-border text-primary" role="status">
            <span class="visually-hidden">Loading...</span>
        </div>
    </div>

    <!-- jQuery + Bootstrap Bundle -->
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>

    <script>
        function showLoading(show) {
            if (show) $("#loading-overlay").css("display", "flex");
            else $("#loading-overlay").hide();
        }

        $("#btnLogin").click(function () {
            var adid = $("#ADID").val().trim();
            var password = $("#password").val().trim();

            if (!adid || !password) {
                Swal.fire({
                    icon: 'warning',
                    title: 'Missing Information',
                    text: 'Please enter both ADID and password.'
                });
                return;
            }

            showLoading(true);

            $.ajax({
                url: '/User/Login',
                type: 'POST',
                contentType: 'application/json',
                data: JSON.stringify({ ADID: adid, Password: password }),
                success: function (response) {
                    showLoading(false);

                    if (response.success) {
                        Swal.fire({
                            icon: 'success',
                            title: 'Login Successful!',
                            text: 'Redirecting to your homepage...',
                            showConfirmButton: false,
                            timer: 1500
                        });

                        setTimeout(function () {
                            window.location.href = '/TPR/Homepage';
                        }, 1500);
                    } else {
                        Swal.fire({
                            icon: 'error',
                            title: 'Login Failed',
                            text: response.message || 'Invalid credentials. Please try again.'
                        });
                    }
                },
                error: function () {
                    showLoading(false);
                    Swal.fire({
                        icon: 'error',
                        title: 'Server Error',
                        text: 'Unable to contact server. Please try again later.'
                    });
                }
            });
        });
    </script>
</body>
</html>




show some good ui message and also loading with good ui
<script>
    $("#btnLogin").click(function () {
        var adid = $("#ADID").val();
        var password = $("#password").val();

        if (!adid || !password) {
            // $("#message").text("Please enter ADID and password.");
            alert("Please enter ADID and password.");
            return;
        }

        $.ajax({
            url: '/User/Login',
            type: 'POST',
            contentType: 'application/json',
            data: JSON.stringify({ ADID: adid, Password: password }),
            success: function (response) {
                if (response.success) {
                    window.location.href = '/TPR/Homepage'; 
                } else {
                    alert("Login failed");
                }
            },
            error: function () {
                $("#message").text("Error contacting server.");
            }
        });
    });
</script>
