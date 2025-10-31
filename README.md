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
