protected void HOD_Record_RowDataBound(object sender, GridViewRowEventArgs e)
{
    if (e.Row.RowType != DataControlRowType.DataRow)
        return;

    bool isDivision   = txtDivision.SelectedIndex > 0;
    bool isDepartment = txtDepartment.SelectedIndex > 0;
    bool isSection    = txtSection.SelectedIndex > 0;

    HtmlGenericControl divDivision =
        e.Row.FindControl("DivDivision") as HtmlGenericControl;
    HtmlGenericControl divDept =
        e.Row.FindControl("Div_Dept") as HtmlGenericControl;
    HtmlGenericControl divSection =
        e.Row.FindControl("DivSection") as HtmlGenericControl;

    // 🔥 DEFAULT: sab hide
    if (divDivision != null) divDivision.Visible = false;
    if (divDept != null) divDept.Visible = false;
    if (divSection != null) divSection.Visible = false;

    
    if (isDivision && divDivision != null)
        divDivision.Visible = true;

    if (isDepartment && divDept != null)
        divDept.Visible = true;

    if (isSection && divSection != null)
        divSection.Visible = true;
}



<script>
    window.appRoot = '@Url.Content("~/")';
</script>

fetch(window.appRoot + 'Master/GetFeedersBySource', {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(sourceIds)
});

$.get(window.appRoot + "PS/GetFeeders",
    { sourceId: sourceId },
    function (feeders) {
        $("#FeederID").html('<option value="">Select Feeder</option>');
        $.each(feeders, function (i, item) {
            $("#FeederID").append('<option value="' + item.id + '">' + item.feederName + '</option>');
        });
    }
);

 $.get(window.appRoot + "PS/GetDTR",
    { sourceId: sourceId, feederId: feederId },
    function (dtrs) {
        $("#DTR").html('<option value="">Select DTR</option>');
        $.each(dtrs, function (i, item) {
            $("#DTR").append('<option value="' + item.id + '">' + item.dtrName + '</option>');
        });
    }
);

html:%20%60%3Cimg%20src=%22$%7Bwindow.appRoot%7Dimages/img9.jpg%22%20width=%22150%22%3E%60,%0A

 
 
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
