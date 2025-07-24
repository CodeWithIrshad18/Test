<script>
    document.getElementById('form2').addEventListener('submit', function (event) {
        event.preventDefault();

        var isValid = true;
        var form = this;
        var elements = form.querySelectorAll('input, select, textarea');

        elements.forEach(function (element) {
            if (['ApprovalFile'].includes(element.id)) {
                return;
            }

            if (element.value.trim() === '') {
                isValid = false;
                element.classList.add('is-invalid');
            } else {
                element.classList.remove('is-invalid');
            }
        });

        if (isValid) {
          
            Swal.fire({
                title: "Uploading...",
                text: "Please wait while your image is being uploaded.",
                didOpen: () => {
                    Swal.showLoading();
                },
                allowOutsideClick: false,
                allowEscapeKey: false
            });

          
            const formData = new FormData(form);

            fetch(form.action, {
                method: 'POST',
                body: formData
            })
                .then(response => {
                    if (response.redirected) {
                        window.location.href = response.url; 
                    } else if (response.ok) {
                        Swal.fire({
                            title: "Success!",
                            text: "Data Saved Successfully",
                            icon: "success",
                            confirmButtonText: "OK"
                        });
                    }
                    
                    else {
                        throw new Error("Upload failed.");
                    }
                })
                .catch(error => {
                    Swal.fire("Error", "There was an error uploading the image: " + error.message, "error");
                });
        }
    });
</script>
