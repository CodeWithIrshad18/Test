<script>

function showError(inputName, message) {
    let input = document.querySelector(`[name="${inputName}"]`);
    let errorSpan = document.querySelector(`span[data-for="${inputName}"]`);
    if (!input) return;

    input.classList.add("is-invalid");
    if (errorSpan) errorSpan.textContent = message;
}

function clearError(inputName) {
    let input = document.querySelector(`[name="${inputName}"]`);
    let errorSpan = document.querySelector(`span[data-for="${inputName}"]`);
    if (!input) return;

    input.classList.remove("is-invalid");
    if (errorSpan) errorSpan.textContent = "";
}

function validateQuarterDate(name, min, max, label) {
    let input = document.querySelector(`[name="${name}"]`);
    if (!input) return true;

    if (input.value.trim() === "") {
        showError(name, `${label} is required`);
        return false;
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
    event.preventDefault();

    let isValid = true;

    let monthlyBlock = document.getElementById("monthlyYearlyFields");
    let quarterlyBlock = document.getElementById("quarterlyFields");

    // Detect visibility
    let monthlyVisible = window.getComputedStyle(monthlyBlock).display !== "none";
    let quarterlyVisible = window.getComputedStyle(quarterlyBlock).display !== "none";

    // --------------------------------------------
    // REQUIRED VALIDATION FOR MONTHLY / YEARLY
    // --------------------------------------------

    if (monthlyVisible) {

        const fields = ["KPISPOC", "ImmediateSuperior", "HOD"];

        fields.forEach(name => {
            let input = document.querySelector(`[name="${name}"]`);

            if (input && input.value.trim() === "") {
                isValid = false;
                input.classList.add("is-invalid");
            } else {
                input.classList.remove("is-invalid");
            }
        });
    }

    // --------------------------------------------
    // REQUIRED + RANGE VALIDATION FOR QUARTERLY
    // --------------------------------------------

    if (quarterlyVisible) {

        isValid = 
            validateQuarterDate("Q1_KPISPOC", "2025-04-01", "2025-06-30", "Q1 KPI SPOC") && isValid &&
            validateQuarterDate("Q1_ImmediateSuperior", "2025-04-01", "2025-06-30", "Q1 Immediate Superior") && isValid &&
            validateQuarterDate("Q1_HOD", "2025-04-01", "2025-06-30", "Q1 HOD") && isValid &&

            validateQuarterDate("Q2_KPISPOC", "2025-07-01", "2025-09-30", "Q2 KPI SPOC") && isValid &&
            validateQuarterDate("Q2_ImmediateSuperior", "2025-07-01", "2025-09-30", "Q2 Immediate Superior") && isValid &&
            validateQuarterDate("Q2_HOD", "2025-07-01", "2025-09-30", "Q2 HOD") && isValid &&

            validateQuarterDate("Q3_KPISPOC", "2025-10-01", "2025-12-31", "Q3 KPI SPOC") && isValid &&
            validateQuarterDate("Q3_ImmediateSuperior", "2025-10-01", "2025-12-31", "Q3 Immediate Superior") && isValid &&
            validateQuarterDate("Q3_HOD", "2025-10-01", "2025-12-31", "Q3 HOD") && isValid &&

            validateQuarterDate("Q4_KPISPOC", "2025-01-01", "2025-03-31", "Q4 KPI SPOC") && isValid &&
            validateQuarterDate("Q4_ImmediateSuperior", "2025-01-01", "2025-03-31", "Q4 Immediate Superior") && isValid &&
            validateQuarterDate("Q4_HOD", "2025-01-01", "2025-03-31", "Q4 HOD") && isValid;
    }

    if (isValid) {
        this.submit();
    }
});
</script>




v <script>
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


 <script>

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


document.getElementById('form').addEventListener('submit', function (event) {

    let valid = true;

    valid = validateQuarterDate("Q1_KPISPOC", "2025-04-01", "2025-06-30", "Q1 KPI SPOC") && valid;
    valid = validateQuarterDate("Q1_ImmediateSuperior", "2025-04-01", "2025-06-30", "Q1 Immediate Superior") && valid;
    valid = validateQuarterDate("Q1_HOD", "2025-04-01", "2025-06-30", "Q1 HOD") && valid;

    valid = validateQuarterDate("Q2_KPISPOC", "2025-07-01", "2025-09-30", "Q2 KPI SPOC") && valid;
    valid = validateQuarterDate("Q2_ImmediateSuperior", "2025-07-01", "2025-09-30", "Q2 Immediate Superior") && valid;
    valid = validateQuarterDate("Q2_HOD", "2025-07-01", "2025-09-30", "Q2 HOD") && valid;

    valid = validateQuarterDate("Q3_KPISPOC", "2025-10-01", "2025-12-31", "Q3 KPI SPOC") && valid;
    valid = validateQuarterDate("Q3_ImmediateSuperior", "2025-10-01", "2025-12-31", "Q3 Immediate Superior") && valid;
    valid = validateQuarterDate("Q3_HOD", "2025-10-01", "2025-12-31", "Q3 HOD") && valid;

    valid = validateQuarterDate("Q4_KPISPOC", "2025-01-01", "2025-03-31", "Q4 KPI SPOC") && valid;
    valid = validateQuarterDate("Q4_ImmediateSuperior", "2025-01-01", "2025-03-31", "Q4 Immediate Superior") && valid;
    valid = validateQuarterDate("Q4_HOD", "2025-01-01", "2025-03-31", "Q4 HOD") && valid;

    if (!valid) {
        event.preventDefault();
    }
});
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
        showError(name, `${label} date must be between range`);
        return false;
    }

    clearError(name);
    return true;
}

 </script>
