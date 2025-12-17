 fetch('/PSD/Master/GetFeedersBySource', {
     method: "POST",
     headers: { "Content-Type": "application/json" },
     body: JSON.stringify(sourceIds) 
 })


 $.get("/PSD/PS/GetFeeders", { sourceId: sourceId }, function (feeders) {

     $("#FeederID").html('<option value="">Select Feeder</option>');
     $.each(feeders, function (i, item) {
         $("#FeederID").append('<option value="' + item.id + '">' + item.feederName + '</option>');
     });

     $("#FeederID").val(feederId).trigger("change");

     
     $.get("/PSD/PS/GetDTR",
         { sourceId: sourceId, feederId: feederId },
         function (dtrs) {

             $("#DTR").html('<option value="">Select DTR</option>');
             $.each(dtrs, function (i, item) {
                 $("#DTR").append('<option value="' + item.id + '">' + item.dtrName + '</option>');
             });

             $("#DTR").val(dtrId).trigger("change");
         }
     );
 });


if (response.success) {
    Swal.fire({
        title: '🎉 Welcome Back!',
        html: '<img src="/PSD/images/img9.jpg" width="150">',
        showConfirmButton: false,
        timer: 3000,
        background: '#f4f6f9',
        backdrop: `
            rgba(0,0,123,0.4)
            url("/images/party.gif")
            left top
            no-repeat
        `
    });

    setTimeout(function () {
        window.location.href = '/PSD/PS/Homepage';
    }, 1800);
}


$.getJSON('/TPR/TPR/GetSections', { division: division, department: dept }, function (data) {
