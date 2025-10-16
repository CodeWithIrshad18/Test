this is my previous js  
<script>
	document.getElementById('form').addEventListener('submit', function (event) {
		event.preventDefault();

		var isValid = true;
		var elements = this.querySelectorAll('input, select, textarea');

		elements.forEach(function (element) {
			if (element.id === 'KPIID'||element.id==='CreatedBy'||element.id==='KPICode') {
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


and this is my dropdown 
<div class="col-md-2">
   
    <select  class="form-control form-control-sm custom-select" id="Externalcomparative">
        <option></option>
        <option value="Available">Available</option>
        <option value="Not Available">Not Available</option>                           
    </select>
</div>

i want when i select Not Available then readonly this two checkbox and also not check the Validation and when user select Available then readonly remove and also validates
 <div class="col-md-2">
     <label for="Externalcomparative" class="control-label">External comparative Value</label>
     </div>

 <div class="col-md-2">
    
    <input class="form-control form-control-sm" id="comparativeValue" autocomplete="off">
 </div>
 <div class="col-md-1">
     <label for="Externalcomparative" class="control-label">Details</label>
     </div>

 <div class="col-md-3">
    <input class="form-control form-control-sm" id="Externalcomparative" autocomplete="off">
 </div>
 
