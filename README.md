<input type="datetime-local" name="Q1_KPISPOC" class="form-control form-control-sm">
<span class="text-danger small error-msg" data-for="Q1_KPISPOC"></span>


function showError(inputName, message) {
    let input = document.querySelector(`[name="${inputName}"]`);
    let errorSpan = document.querySelector(`span[data-for="${inputName}"]`);

    if (!errorSpan) return;

    input.classList.add("is-invalid");
    errorSpan.textContent = message;
}

function clearError(inputName) {
    let input = document.querySelector(`[name="${inputName}"]`);
    let errorSpan = document.querySelector(`span[data-for="${inputName}"]`);

    if (!errorSpan) return;

    input.classList.remove("is-invalid");
    errorSpan.textContent = "";
}

function validateQuarterDate(name, min, max, label) {
    let input = document.querySelector(`[name="${name}"]`);
    if (!input || input.value === "") {
        clearError(name);
        return true;
    }

    let date = new Date(input.value);
    let minDate = new Date(min);
    let maxDate = new Date(max);

    if (date < minDate || date > maxDate) {
        showError(name, `${label} must be between ${min} and ${max}`);
        return false;
    }

    clearError(name);
    return true;
}


document.getElementById('form').addEventListener('submit', function (event) {

    let valid = true;

    valid = validateQuarterDate("Q1_KPISPOC", "2025-04-01", "2025-06-30", "Q1 KPI SPOC") && valid;
    valid = validateQuarterDate("Q1_Superior", "2025-04-01", "2025-06-30", "Q1 Immediate Superior") && valid;
    valid = validateQuarterDate("Q1_HOD", "2025-04-01", "2025-06-30", "Q1 HOD") && valid;

    valid = validateQuarterDate("Q2_KPISPOC", "2025-07-01", "2025-09-30", "Q2 KPI SPOC") && valid;
    valid = validateQuarterDate("Q2_Superior", "2025-07-01", "2025-09-30", "Q2 Immediate Superior") && valid;
    valid = validateQuarterDate("Q2_HOD", "2025-07-01", "2025-09-30", "Q2 HOD") && valid;

    valid = validateQuarterDate("Q3_KPISPOC", "2025-10-01", "2025-12-31", "Q3 KPI SPOC") && valid;
    valid = validateQuarterDate("Q3_Superior", "2025-10-01", "2025-12-31", "Q3 Immediate Superior") && valid;
    valid = validateQuarterDate("Q3_HOD", "2025-10-01", "2025-12-31", "Q3 HOD") && valid;

    valid = validateQuarterDate("Q4_KPISPOC", "2025-01-01", "2025-03-31", "Q4 KPI SPOC") && valid;
    valid = validateQuarterDate("Q4_Superior", "2025-01-01", "2025-03-31", "Q4 Immediate Superior") && valid;
    valid = validateQuarterDate("Q4_HOD", "2025-01-01", "2025-03-31", "Q4 HOD") && valid;

    if (!valid) {
        event.preventDefault();
    }
});






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
