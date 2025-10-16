<div class="external-comparative-group">
    <div class="col-md-2">
        <label class="control-label">External Comparative</label>
    </div>
    <div class="col-md-2">
        <select class="form-control form-control-sm custom-select external-comparative-select">
            <option value=""></option>
            <option value="Available">Available</option>
            <option value="Not Available">Not Available</option>
        </select>
    </div>

    <div class="col-md-2">
        <label class="control-label">External Comparative Value</label>
    </div>
    <div class="col-md-2">
        <input class="form-control form-control-sm comparative-value" autocomplete="off">
    </div>

    <div class="col-md-1">
        <label class="control-label">Details</label>
    </div>
    <div class="col-md-3">
        <input class="form-control form-control-sm comparative-details" autocomplete="off">
    </div>
</div>

<script>
document.addEventListener('DOMContentLoaded', function () {
    const form = document.getElementById('form');
    const groups = document.querySelectorAll('.external-comparative-group');

    // Attach change event to each group's dropdown
    groups.forEach(group => {
        const dropdown = group.querySelector('.external-comparative-select');
        const valueInput = group.querySelector('.comparative-value');
        const detailsInput = group.querySelector('.comparative-details');

        dropdown.addEventListener('change', function () {
            if (this.value === 'Not Available') {
                valueInput.readOnly = true;
                detailsInput.readOnly = true;

                valueInput.value = '';
                detailsInput.value = '';

                valueInput.classList.remove('is-invalid');
                detailsInput.classList.remove('is-invalid');
            } else {
                valueInput.readOnly = false;
                detailsInput.readOnly = false;
            }
        });
    });

    // Global form validation
    form.addEventListener('submit', function (event) {
        event.preventDefault();
        let isValid = true;
        const elements = this.querySelectorAll('input, select, textarea');

        elements.forEach(function (element) {
            // Skip IDs you don’t want to validate
            if (['KPIID', 'CreatedBy', 'KPICode'].includes(element.id)) return;

            // Skip read-only / disabled elements
            if (element.readOnly || element.disabled) {
                element.classList.remove('is-invalid');
                return;
            }

            // Validate value
            if (element.value.trim() === '') {
                isValid = false;
                element.classList.add('is-invalid');
            } else {
                element.classList.remove('is-invalid');
            }
        });

        if (isValid) {
            form.submit();
        }
    });
});
</script>




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
 
