<script>
document.getElementById('form').addEventListener('submit', function (event) {
    event.preventDefault();

    var isValid = true;
    var elements = this.querySelectorAll('input, select, textarea');

    // Get mode and date fields
    var activeCheckbox = document.getElementById('Active');
    var inactiveCheckbox = document.getElementById('Inactive');
    var deactiveFrom = document.getElementById('DeactivateFrom');
    var deactiveTo = document.getElementById('DeactivateTo');

    elements.forEach(function (element) {
        // Skip specific fields
        if (element.id === 'KPIID' || element.id === 'CreatedBy' || element.id === 'KPICode') {
            return;
        }

        // Skip DeactivateFrom/To if Active mode is selected
        if ((element.id === 'DeactivateFrom' || element.id === 'DeactivateTo') && activeCheckbox.checked) {
            element.classList.remove('is-invalid');
            return;
        }

        // Regular validation
        if (element.value.trim() === '') {
            isValid = false;
            element.classList.add('is-invalid');
        } else {
            element.classList.remove('is-invalid');
        }
    });

    // Submit only if all fields valid
    if (isValid) {
        this.submit();
    }
});
</script>

 
 
 
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
