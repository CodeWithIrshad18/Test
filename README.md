<script>
document.getElementById('form').addEventListener('submit', function (event) {
    event.preventDefault();

    var isValid = true;
    var elements = this.querySelectorAll('input, select, textarea');
    var holdValue = document.querySelector('input[name="Hold"]:checked')?.value;

    elements.forEach(function (element) {

        if (element.id === 'KPIID' || element.id === 'Period') {
            return;
        }


        if (element.readOnly || element.disabled) {
            element.classList.remove('is-invalid');
            return;
        }

 
        if (holdValue === "true") {
           
            if (element.id === 'Value' || element.id === 'YTDValue') {
                element.classList.remove('is-invalid');
                return;
            }
            if (element.id === 'HoldReason' && element.value.trim() === '') {
                isValid = false;
                element.classList.add('is-invalid');
                return;
            }
        } else {
          
            if (element.id === 'HoldReason') {
                element.classList.remove('is-invalid');
                return;
            }
            if ((element.id === 'Value' || element.id === 'YTDValue') && element.value.trim() === '') {
                isValid = false;
                element.classList.add('is-invalid');
                return;
            }
        }

        if (element.value.trim() === '') {
            isValid = false;
            element.classList.add('is-invalid');
        } else {
            element.classList.remove('is-invalid');
        }
    });

    if (isValid) {
        this.submit();
    }
});
</script>


 <div class="col-md-3">
   <label for="Target" class="control-label">Target</label>
 </div>
 <div class="col-md-1">
   <input type="text" class="form-control form-control-sm" id="Target" autocomplete="off" readonly>
 </div>
