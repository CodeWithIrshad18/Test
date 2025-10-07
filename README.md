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
