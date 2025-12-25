function ValidateHodRatings(src, args) {

        var table = document.getElementById("<%= Plandetails.ClientID %>");
        var rows = table.getElementsByTagName("tr");

        // i = 1  ? header skip
        for (var i = 1; i < rows.length; i++) {
            // sirf is row ke radio lo
            var radios = rows[i].querySelectorAll('input[type=radio][name*="HOD_Rating"]');

            if (radios.length === 0) continue;   // agar row me radio hi nahi hai to skip

            var checked = false;

            for (var j = 0; j < radios.length; j++) {
                if (radios[j].checked) {
                    checked = true;
                    break;
                }
            }

            // ? agar is row me kuch select nahi hai ? INVALID
            if (!checked) {
                args.IsValid = false;
                return;
            }
        }

        // ? sab rows valid
        args.IsValid = true;
    }
