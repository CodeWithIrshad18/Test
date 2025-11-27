<script>
document.getElementById('form').addEventListener('submit', function (event) {
    event.preventDefault();

    let isValid = true;

    let monthlyBlock = document.getElementById("monthlyYearlyFields");
    let quarterlyBlock = document.getElementById("quarterlyFields");

    let elements;

    // ✔ If monthly/yearly is visible → validate only those
    if (monthlyBlock.style.display !== "none") {

        elements = monthlyBlock.querySelectorAll('input, select, textarea');

    }
    // ✔ If quarterly is visible → validate only quarterly
    else if (quarterlyBlock.style.display !== "none") {

        elements = quarterlyBlock.querySelectorAll('input, select, textarea');

    }
    // fallback – no block visible
    else {
        elements = [];
    }

    // Loop over selected elements
    elements.forEach(function (element) {

        // skip hidden system fields
        if (element.id === 'PeriodicityId' || element.id === 'CreatedBy') {
            return;
        }

        if (element.value.trim() === '') {
            isValid = false;
            element.classList.add('is-invalid');
        } 
        else {
            element.classList.remove('is-invalid');
        }
    });

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
