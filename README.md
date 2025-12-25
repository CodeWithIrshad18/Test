<!-- ASP.NET CustomValidator -->
<asp:CustomValidator ID="cvHodRatings" runat="server"
    ClientValidationFunction="ValidateHodRatings"
    ErrorMessage="Please select ratings for all rows."
    Display="None" ValidationGroup="vgSubmit" />

<!-- ValidationSummary to show error -->
<asp:ValidationSummary ID="vsSummary" runat="server"
    ValidationGroup="vgSubmit" ShowMessageBox="false" ShowSummary="true" />

<!-- Inline error placeholder -->
<div id="errorMsg" style="color:red; font-weight:bold;"></div>

<script type="text/javascript">
    function ValidateHodRatings(src, args) {
        var table = document.getElementById("<%= Plandetails.ClientID %>");
        var rows = table.getElementsByTagName("tr");

        // clear previous highlights
        for (var r = 1; r < rows.length; r++) {
            rows[r].style.backgroundColor = "";
        }
        document.getElementById("errorMsg").innerText = "";

        for (var i = 1; i < rows.length; i++) {
            var radios = rows[i].querySelectorAll('input[type=radio][name*="HOD_Rating"]');
            if (radios.length === 0) continue;

            var checked = false;
            for (var j = 0; j < radios.length; j++) {
                if (radios[j].checked) {
                    checked = true;
                    break;
                }
            }

            if (!checked) {
                // mark invalid row
                rows[i].style.backgroundColor = "#ffe0e0"; // light red
                // show inline error
                document.getElementById("errorMsg").innerText = "Please select ratings for all rows.";
                args.IsValid = false;
                return;
            }
        }

        args.IsValid = true;
    }
</script>




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
