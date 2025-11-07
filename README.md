and this is my controller code  
if (isFaceMatched)
 {
     if (model.Type == "Punch In")
     {
         string newCapturedPath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot/Images/", $"{Pno}-Captured.jpg");
         SaveBase64ImageToFile(model.ImageData, newCapturedPath);

         StoreData(currentDate, currentTime, null, Pno);
         if (pyrlArea == "JS" || pyrlArea == "ZZ")
         StoreDataNOPR(currentDate, currentTime, null, Pno);
         StoreVersionDetails(Pno, null, currentTime);
             record.PunchInSuccess = true;
     }
     else
     {
         StoreData(currentDate, null, currentTime, Pno);
         if (pyrlArea == "JS" || pyrlArea == "ZZ")
         StoreDataNOPR(currentDate, null, currentTime, Pno);
         StoreVersionDetails(Pno,null,currentTime);
         record.PunchOutSuccess = true;
     }

     context.SaveChanges();
     return Json(new { success = true, message = "Attendance recorded successfully." });
 }


 public void StoreVersionDetails(string Pno,string tmIn, string tmOut)
 {
     using (var connection = new SqlConnection(configuration.GetConnectionString("RFID")))
     {
         connection.Open();

         if (!string.IsNullOrEmpty(tmIn))
         {
             var query = @"
         INSERT INTO App_VersionDetail(Pno, Version,PunchIN) 
         VALUES 
         (@Pno,
         @Version
         @PunchIN)";

             var parameters = new
             {
                 Pno = Pno,
                 Version = "OLD VERSION",
                 PunchIN = "I",
             };

             connection.Execute(query, parameters);
         }
         if (!string.IsNullOrEmpty(tmOut))
         {
             var query = @"
         INSERT INTO App_VersionDetail(Pno, Version,PunchOUT) 
         VALUES 
         (@Pno,
         @Version
         @PunchOUT)";

             var parameters = new
             {
                 Pno = Pno,
                 Version = "OLD VERSION",
                 PunchOUT = "O",
             };

             connection.Execute(query, parameters);
         }
     }
 }

this is my js 


<script>
    const video = document.getElementById("video");
    const canvas = document.getElementById("canvas");
    const EntryTypeInput = document.getElementById("EntryType");
   

    navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } })
        .then(function (stream) {
            let video = document.getElementById("video");
            video.srcObject = stream;
            video.play();
        })
        .catch(function (error) {
            console.error("Error accessing camera: ", error);
        });

 
    function captureImageAndSubmit(entryType) {
        EntryTypeInput.value = entryType;

        const context = canvas.getContext("2d");
        canvas.width = video.videoWidth;
        canvas.height = video.videoHeight;
        context.drawImage(video, 0, 0, canvas.width, canvas.height);

        const imageData = canvas.toDataURL("image/jpeg"); // Save as JPG

        
        Swal.fire({
            title: "Verifying Face...",
            allowOutsideClick: false,
            showConfirmButton: false,
            didOpen: () => {
                Swal.showLoading();
            }
        });

       
       

        fetch("/TSUISLARS/Geo/AttendanceData", {
            method: "POST",
            headers: {
                "Content-Type": "application/json"
            },
            body: JSON.stringify({
                Type: entryType,
                ImageData: imageData
            })
        })
            .then(response => response.json())
            .then(data => {
                if (data.success) {
                    var now = new Date();
                    var formattedDateTime = now.toLocaleString();  
                    triggerHapticFeedback("success");

                    Swal.fire({
                        title: "Face Matched!",
                        text: "Attendance Recorded.\nDate & Time: " + formattedDateTime,
                        icon: "success",
                        timer: 3000,
                        showConfirmButton: false
                    }).then(() => {
                        location.reload();  
                    }); 

                } else {      
                    triggerHapticFeedback("error");
                    var now = new Date();
                    var formattedDateTime = now.toLocaleString();
                    Swal.fire({
                        title: "Face Not Recognized.",
                        text: "Click the button again to retry.\nDate & Time: " + formattedDateTime,
                        icon: "error",
                        confirmButtonText: "Retry"
                    });
                }
            })
            .catch(error => {
                console.error("Error:", error);
                triggerHapticFeedback("error");

                Swal.fire({
                    title: "Error!",
                    text: "An error occurred while processing your request.",
                    icon: "error"
                });
            });
            
    }

    function triggerHapticFeedback(type) {
        if ("vibrate" in navigator) {
            if (type === "success") {
                navigator.vibrate(100); 
            } else if (type === "error") {
                navigator.vibrate([200, 100, 200]); 
            }
        }
    }
</script>

this is my table 

CREATE TABLE [dbo].[App_VersionDetail] (
    [ID]          UNIQUEIDENTIFIER DEFAULT (newid()) NOT NULL,
    [Pno]         VARCHAR (6)      NULL,
    [Version]     VARCHAR (25)     NULL,
    [DateAndTime] DATETIME         DEFAULT (getdate()) NULL,
    [PunchIN]     VARCHAR (1)      DEFAULT (NULL) NULL,
    [PunchOUT]    VARCHAR (1)      DEFAULT (NULL) NULL,
    PRIMARY KEY CLUSTERED ([ID] ASC)
);


i want when storing of version it stores the if PunchIn I otherwise O
