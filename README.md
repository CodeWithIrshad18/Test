<script>
document.addEventListener('DOMContentLoaded', function () {
    const form = document.getElementById('form');
    const groups = document.querySelectorAll('.external-comparative-group');
    const refNoLinks = document.querySelectorAll('.refNoLink');

    // 🔹 function to apply readonly logic based on dropdown
    function applyReadOnlyLogic(group) {
        const dropdown = group.querySelector('.external-comparative-select');
        const valueInput = group.querySelector('.comparative-value');
        const detailsInput = group.querySelector('.comparative-details');

        if (!dropdown || !valueInput || !detailsInput) return;

        if (dropdown.value === 'No') {
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
    }

    // 🔹 Apply logic on dropdown change
    groups.forEach(group => {
        const dropdown = group.querySelector('.external-comparative-select');
        dropdown.addEventListener('change', function () {
            applyReadOnlyLogic(group);
        });
    });

    // 🔹 Apply logic when clicking refNoLink
    refNoLinks.forEach(link => {
        link.addEventListener('click', function (event) {
            event.preventDefault();

            // Loop through all groups and reapply readonly logic
            groups.forEach(group => {
                applyReadOnlyLogic(group);
            });

            // Optional: do your refNoLink click logic here (like opening modal)
            console.log("refNoLink clicked, readonly logic reapplied");
        });
    });

    // 🔹 Validation on submit
    form.addEventListener('submit', function (event) {
        event.preventDefault();
        let isValid = true;
        const elements = this.querySelectorAll('input, select, textarea');

        elements.forEach(function (element) {
            if (['KPIID', 'CreatedBy', 'KPICode', 'ID'].includes(element.id)) return;

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
            form.submit();
        }
    });
});
</script>





this is my js i want that when i click on refNoLink then check for that also 


<script>
document.addEventListener('DOMContentLoaded', function () {
    const form = document.getElementById('form');
    const groups = document.querySelectorAll('.external-comparative-group');

    groups.forEach(group => {
        const dropdown = group.querySelector('.external-comparative-select');
        const valueInput = group.querySelector('.comparative-value');
        const detailsInput = group.querySelector('.comparative-details');

       

        dropdown.addEventListener('change', function () {
            if (this.value === 'No') {
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

 
    form.addEventListener('submit', function (event) {
        event.preventDefault();
        let isValid = true;
        const elements = this.querySelectorAll('input, select, textarea');

        elements.forEach(function (element) {

            if (['KPIID', 'CreatedBy', 'KPICode','ID'].includes(element.id)) return;

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
            form.submit();
        }
    });
});
</script>

 <a href="#"
 class="refNoLink"
</a>
