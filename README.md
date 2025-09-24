<button type="button" class="close text-danger" data-dismiss="modal" aria-label="Close">
  <span aria-hidden="true">&times;</span>
</button>



i have this full code , in this model close button is not working , please look into this                
 <form asp-action="UploadImage" method="post" id="form2">

                    <input type="hidden" id="PasswordHidden" name="password" />

<div class="modal fade" id="passwordModal" tabindex="-1" role="dialog">
  <div class="modal-dialog modal-dialog-centered">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title">Enter Password</h5>
        <button type="button" class="close btn btn-danger" data-dismiss="modal" style="padding:2px 9px 2px 8px;" >&times;</button>
      </div>
      <div class="modal-body">
        <input type="password" id="PasswordInput" class="form-control" placeholder="Enter your password" autocomplete="off"/>
      </div>
      <div class="modal-footer">
        <button type="button" id="confirmPasswordBtn" class="btn btn-success">Confirm</button>
      </div>
    </div>
  </div>
</div>
                    <div class="form-group row">
                        <div class="col-sm-1">
                            <label>Pno</label>
                        </div>
                        <div class="col-sm-3">
                            <input id="Pno" name="Pno" class="form-control" type="number" value="@ViewBag.Pno" oninput="javascript: if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);" maxlength="6" autocomplete="off" readonly/>
                        </div>
                        <div class="col-sm-1">
                            <label>Name</label>
                        </div>
                        <div class="col-sm-3">
                            <input id="Name" name="Name" class="form-control" value="@ViewBag.Name" readonly/>
                        </div>
                        <div class="col-sm-1">
                            <label>Capture Photo</label>
                        </div>
                        <div class="col-sm-3">
                            <video id="video" width="320" height="240" autoplay playsinline></video>
                            <canvas id="canvas" style="display:none;"></canvas>

                          
                            <img id="previewImage" src="" alt="Captured Image" style="width: 200px; display: none; border: 2px solid black; margin-top: 5px;" />

                           
                            <button type="button" id="captureBtn" class="btn btn-primary">Capture</button>
                            <button type="button" id="retakeBtn" class="btn btn-danger" style="display: none;">Retake</button>
                            <a asp-action="GeoFencing" asp-controller="Geo" class="control-label btn btn-warning"><i class="fa fa-arrow-left" aria-hidden="true" style="font-size:16px;"></i>&nbsp;&nbsp;Back</a>
                           
                            <input type="hidden" id="photoData" name="photoData" />
                        </div>
                    </div>

                    <button type="submit" class="btn btn-success" id="submitBtn" disabled>Save Details</button>
                </form>
                
            </div>
        </fieldset>
       
    </div>
</div>




<script>
   
    navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } })
        .then(function (stream) {
            let video = document.querySelector("video");
            video.srcObject = stream;
            video.play();
        })
        .catch(function (error) {
            console.error("Error accessing camera: ", error);
        });

   


    document.getElementById("captureBtn").addEventListener("click", function () {
        let video = document.getElementById("video");
        let canvas = document.getElementById("canvas");
        let context = canvas.getContext("2d");

       
        canvas.width = video.videoWidth;
        canvas.height = video.videoHeight;
        context.drawImage(video, 0, 0, canvas.width, canvas.height);

        context.translate(canvas.width, 0);
        context.scale(-1, 1);
        context.drawImage(video, 0, 0, canvas.width, canvas.height);
        context.setTransform(1, 0, 0, 1, 0, 0);

       
        let imageData = canvas.toDataURL("image/png");
        document.getElementById("previewImage").src = imageData;
        document.getElementById("previewImage").style.display = "block";
        document.getElementById("photoData").value = imageData;

        
        video.style.display = "none";
        document.getElementById("captureBtn").style.display = "none";
        document.getElementById("retakeBtn").style.display = "inline-block";
        document.getElementById("submitBtn").disabled = false; 
    });

    
    document.getElementById("retakeBtn").addEventListener("click", function () {
        let video = document.getElementById("video");

        
        video.style.display = "block";
        document.getElementById("captureBtn").style.display = "inline-block";
        document.getElementById("retakeBtn").style.display = "none";
        document.getElementById("previewImage").style.display = "none";
        document.getElementById("submitBtn").disabled = true; 
    });

    
</script>

<script>
   
    var pnoEnameList = @Html.Raw(JsonConvert.SerializeObject(ViewBag.PnoEnameList));


    document.addEventListener("DOMContentLoaded", function () {
        document.getElementById("Pno").addEventListener("input", function () {
            var pno = this.value;


            var user = pnoEnameList.find(u => u.Pno === pno);

            if (user) {
                document.getElementById("Name").value = user.Ename;

            } else {
                document.getElementById("Name").value = "";

            }



        });
    });

</script>


<script>
    const form = document.getElementById("form2");
    const passwordHidden = document.getElementById("PasswordHidden");
    const passwordInput = document.getElementById("PasswordInput");

    form.addEventListener("submit", function (e) {
        e.preventDefault();
        const pno = document.getElementById("Pno").value;

        fetch('/Geo/CheckIfExists?pno=' + pno)
            .then(res => res.json())
            .then(data => {
                if (data.exists) {
                   
                    passwordInput.value = "";

                    
                    $('#passwordModal').modal('show');
                } else {
                   
                    submitForm(form);
                }
            });
    });

   
    document.getElementById("confirmPasswordBtn").addEventListener("click", function () {
        const enteredPassword = passwordInput.value.trim();

        if (!enteredPassword) {
            Swal.fire("Warning", "Please enter your password.", "warning");
            return;
        }

        passwordHidden.value = enteredPassword;

        
        $('#passwordModal').modal('hide');

      
        submitForm(form);
    });

    function submitForm(form) {
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
            .then(async response => {
                const result = await response.json().catch(() => ({}));

                if (response.ok && result.success) {
                    Swal.fire({
                        title: "Success!",
                        text: result.message || "Data Saved Successfully",
                        icon: "success",
                        confirmButtonText: "OK"
                    });
                } else if (response.status === 401) {
                    Swal.fire("Unauthorized", result.message || "Invalid password, update denied.", "error");
                } else {
                    Swal.fire("Error", result.message || "Upload failed.", "error");
                }
            })
            .catch(error => {
                Swal.fire("Error", "There was an error uploading the image: " + error.message, "error");
            });
    }
</script>
