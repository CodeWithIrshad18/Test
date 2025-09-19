// instead of: var query = context.AppPositionWorksites.AsQueryable();

var query = from pw in context.AppPositionWorksites
            join ep in context.AppEmpPositions on pw.Position equals ep.Position
            select new
            {
                pw.Id,
                pw.Position,
                pw.Worksite,
                ep.Pno   // 👈 Now we also include Pno
            };




var worksiteArray = response.worksite
    .split(',')
    .map(x => x.trim().toUpperCase())  // normalize to uppercase
    .filter(x => x !== "");

$('.worksite-checkbox').each(function () {
    var checkboxVal = $(this).val().toUpperCase();
    $(this).prop('checked', worksiteArray.includes(checkboxVal));
});


console.log("Raw response.worksite:", response.worksite);
console.log("After split:", response.worksite.split(','));



these are in my db
428614DE-BCBB-4310-B13F-8080D973C6D2,D5397476-DDA4-466C-8C0E-67BC57CF83B9,E5A35ECD-D84F-4912-9DC8-D0209F5F6A03

and these are input type and these are not checked in my view side
<input type="checkbox" class="form-check-input worksite-checkbox" value="428614DE-BCBB-4310-B13F-8080D973C6D2" id="worksite_428614DE-BCBB-4310-B13F-8080D973C6D2">
<input type="checkbox" class="form-check-input worksite-checkbox" value="D5397476-DDA4-466C-8C0E-67BC57CF83B9" id="worksite_D5397476-DDA4-466C-8C0E-67BC57CF83B9">
<input type="checkbox" class="form-check-input worksite-checkbox" value="E5A35ECD-D84F-4912-9DC8-D0209F5F6A03" id="worksite_E5A35ECD-D84F-4912-9DC8-D0209F5F6A03">
