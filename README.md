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
