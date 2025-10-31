<div class="alert alert-info d-flex align-items-center mt-3" role="alert">
  <i class="bi bi-info-circle me-2"></i>
  <div>
    Once target is set, it cannot be modified.
  </div>
</div>

 <div class="info-box">
  <span class="info-icon">ℹ️</span>
  <p>Once target is set, it cannot be modified.</p>
</div>

<style>
.info-box {
  display: flex;
  align-items: center;
  gap: 10px;
  background: #e8f4fd;
  color: #084298;
  border-left: 5px solid #0d6efd;
  border-radius: 6px;
  padding: 12px 16px;
  font-family: 'Segoe UI', sans-serif;
  font-size: 14px;
  max-width: 400px;
}
.info-icon {
  font-size: 20px;
}
</style>

<div class="info-card">
  <h5>Important</h5>
  <p>Once target is set, it cannot be modified. Please double-check before saving.</p>
</div>

<style>
.info-card {
  background-color: #f1f9ff;
  border: 1px solid #b6e0fe;
  border-radius: 8px;
  padding: 15px;
  max-width: 420px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.08);
}
.info-card h5 {
  margin: 0 0 6px 0;
  color: #0b5ed7;
  font-weight: 600;
}
.info-card p {
  margin: 0;
  color: #084298;
  font-size: 14px;
}
</style>



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
