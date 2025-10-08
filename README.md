<!-- Add this in your layout head -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/notyf@3/notyf.min.css">
<script src="https://cdn.jsdelivr.net/npm/notyf@3/notyf.min.js"></script>

<script>
    const notyf = new Notyf({
        duration: 2500,
        ripple: true,
        position: { x: 'center', y: 'top' },
        types: [
            { type: 'success', background: '#4caf50', icon: false },
            { type: 'error', background: '#f44336', icon: false }
        ]
    });

    $(document).on("keypress", function (e) {
        if (e.which === 13) $("#btnLogin").click();
    });

    $("#btnLogin").click(function () {
        var adid = $("#ADID").val().trim();
        var password = $("#password").val().trim();

        if (!adid || !password) {
            notyf.error('Please enter both ADID and password.');
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
                    notyf.success('🎉 Welcome back!');
                    setTimeout(() => window.location.href = '/TPR/Homepage', 1500);
                } else {
                    notyf.error(response.message || 'Invalid credentials.');
                }
            },
            error: function () {
                showLoading(false);
                notyf.error('Unable to contact server.');
            }
        });
    });
</script>


<style>
    #loading-overlay {
        position: fixed;
        top: 0; left: 0;
        width: 100%; height: 100%;
        background: rgba(255, 255, 255, 0.8);
        display: none;
        justify-content: center;
        align-items: center;
        z-index: 9999;
    }

    .spinner {
        width: 60px;
        height: 60px;
        border: 6px solid #ddd;
        border-top: 6px solid #3498db;
        border-radius: 50%;
        animation: spin 1s linear infinite;
    }

    @keyframes spin {
        to { transform: rotate(360deg); }
    }
</style>

<div id="loading-overlay">
    <div class="spinner"></div>
</div>

<script>
    function showLoading(show) {
        if (show) $("#loading-overlay").css("display", "flex");
        else $("#loading-overlay").hide();
    }

    // 🔹 Trigger login when Enter is pressed
    $(document).on("keypress", function (e) {
        if (e.which === 13) {
            $("#btnLogin").click();
        }
    });

    $("#btnLogin").click(function () {
        var adid = $("#ADID").val().trim();
        var password = $("#password").val().trim();

        if (!adid || !password) {
            Swal.fire({
                icon: 'warning',
                title: 'Missing Information',
                text: 'Please enter both ADID and password.',
                confirmButtonColor: '#3085d6'
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
                        title: '🎉 Welcome Back!',
                        html: '<img src="/images/welcome.gif" width="120"><br><b>Redirecting to your dashboard...</b>',
                        showConfirmButton: false,
                        timer: 1800,
                        background: '#f4f6f9',
                        backdrop: `
                            rgba(0,0,123,0.4)
                            url("/images/party.gif")
                            left top
                            no-repeat
                        `
                    });

                    setTimeout(function () {
                        window.location.href = '/TPR/Homepage';
                    }, 1800);
                } else {
                    Swal.fire({
                        icon: 'error',
                        title: 'Login Failed 😔',
                        text: response.message || 'Invalid credentials. Please try again.',
                        confirmButtonColor: '#d33'
                    });
                }
            },
            error: function () {
                showLoading(false);
                Swal.fire({
                    icon: 'error',
                    title: 'Server Error ⚠️',
                    text: 'Unable to contact server. Please try again later.'
                });
            }
        });
    });
</script>
 
 
 
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
