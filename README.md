<script>
	document.getElementById('form').addEventListener('submit', function (event) {
		event.preventDefault();

		var isValid = true;
		var elements = this.querySelectorAll('input, select, textarea');

       

		elements.forEach(function (element) {
			if (element.id === 'KPIID'||element.id==='Period'||element.id==='HoldReason'||element.id==='YTDValue'||element.id==='Value') {
				return;
			}

             if (element.readOnly || element.disabled) {
                element.classList.remove('is-invalid');
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
			
				this.submit();
			
		}
	});
</script>


 <div class="col-md-1">
     <label for="HOLD" class="control-label">HOLD</label>
 </div>

 <div class="col-md-1">
     <div class="form-check">
         <input type="radio" class="form-check-input" name="Hold" id="NO" value="false" autocomplete="off" checked>
         <label class="form-check-label" for="NO">NO</label>
     </div>
 </div>

 <div class="col-md-1">
     <div class="form-check">
         <input type="radio" class="form-check-input" name="Hold" id="YES" value="true" autocomplete="off">
         <label class="form-check-label" for="YES">YES</label>
     </div>
 </div>


<input asp-for="Value" type="text" class="form-control form-control-sm" id="Value" autocomplete="off">
 <input asp-for="YTDValue" type="text" class="form-control form-control-sm" id="YTDValue" autocomplete="off">

 <input asp-for="HoldReason" class="form-control form-control-sm" id="HoldReason" name="HoldReason" autocomplete="off">
