this is my form                 
<form asp-action="UploadImage" method="post" id="form2">

                    <input type="hidden" id="PasswordHidden" name="password" />

<div class="modal fade" id="passwordModal" tabindex="-1" role="dialog">
  <div class="modal-dialog modal-dialog-centered">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title">Enter Password</h5>
        <button type="button" class="close btn btn-danger" data-dismiss="modal" style="padding:2px 9px 2px 8px;">&times;</button>
      </div>
      <div class="modal-body">
        <input type="password" id="PasswordInput" class="form-control" placeholder="Enter your password" />
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

this is js 
<script>
   document.getElementById("form2").addEventListener("submit", function (e) {
    e.preventDefault(); 
    var form = this;
    var pno = document.getElementById("Pno").value;

    fetch('/Geo/CheckIfExists?pno=' + pno)
        .then(res => res.json())
        .then(data => {
            if (data.exists) {
               
                $('#passwordModal').modal('show');
            } else {
               
                submitForm(form);
            }
        });
});

document.getElementById("confirmPasswordBtn").addEventListener("click", function () {
    var enteredPassword = document.getElementById("PasswordInput").value;
    document.getElementById("PasswordHidden").value = enteredPassword;

    $('#passwordModal').modal('hide');
    submitForm(document.getElementById("form2"));
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
        .then(response => {
            if (response.ok) {
                Swal.fire({
                    title: "Success!",
                    text: "Data Saved Successfully",
                    icon: "success",
                    confirmButtonText: "OK"
                });
            } else if (response.status === 401) {
                Swal.fire("Unauthorized", "Invalid password, update denied.", "error");
            } else {
                throw new Error("Upload failed.");
            }
        })
        .catch(error => {
            Swal.fire("Error", "There was an error uploading the image: " + error.message, "error");
        });
}

 </script>

make changes I want according to this 
