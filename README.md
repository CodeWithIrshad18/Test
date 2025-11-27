 <script>
	document.getElementById('form').addEventListener('submit', function (event) {
		event.preventDefault();

		var isValid = true;
		var elements = this.querySelectorAll('input, select, textarea');

		elements.forEach(function (element) {
			if (element.id === 'PeriodicityId'||element.id==='CreatedBy') {
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


<div id="quarterlyFields" style="display:none;">

 </div>

<div id="monthlyYearlyFields" class="row g-3 mt-2" style="display:none;">

</div>
