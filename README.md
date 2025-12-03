i want when page is load by default comparative is No selected
<script>
document.addEventListener('DOMContentLoaded', function () {
    const form = document.getElementById('form');
    const groups = document.querySelectorAll('.external-comparative-group');
    const refNoLinks = document.querySelectorAll('.refNoLink');
    const yesRadio = document.getElementById('Yes');
    const noRadio = document.getElementById('No');



    function toggleComparativeGroups() {
        if (yesRadio.checked) {
   
            groups.forEach(group => group.style.display = 'block');
        } else if (noRadio.checked) {
  
            groups.forEach(group => {
                group.style.display = 'none';

   
                const dropdown = group.querySelector('.external-comparative-select');
                const valueInput = group.querySelector('.comparative-value');
                const detailsInput = group.querySelector('.comparative-details');

                if (dropdown) {
                    dropdown.value = 'No';
                }
                if (valueInput) {
                    valueInput.value = '';
                    valueInput.readOnly = true;
                }
                if (detailsInput) {
                    detailsInput.value = '';
                    detailsInput.readOnly = true;
                }
            });
        }
    }


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


    toggleComparativeGroups();


    yesRadio.addEventListener('change', toggleComparativeGroups);
    noRadio.addEventListener('change', toggleComparativeGroups);

    groups.forEach(group => {
        const dropdown = group.querySelector('.external-comparative-select');
        if (dropdown) {
            dropdown.addEventListener('change', function () {
                applyReadOnlyLogic(group);
            });
        }
    });


    refNoLinks.forEach(link => {
        link.addEventListener('click', function (event) {
            event.preventDefault();
            groups.forEach(group => applyReadOnlyLogic(group));
        });
    });

    form.addEventListener('submit', function (event) {
        event.preventDefault();
        let isValid = true;
        const elements = this.querySelectorAll('input, select, textarea');

        elements.forEach(function (element) {
            if (['KPIID', 'CreatedBy', 'KPICode', 'ID', 'value', 'Periodicity'].includes(element.id)) return;

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


                     <div class="row g-3 mt-1 mb-3 align-items-center">
    <div class="col-md-2">
        <label for="comparative" class="control-label">Comparative</label>
    </div>

    <div class="col-md-1">
        <div class="form-check">
            <input type="radio" class="form-check-input" name="Comparative" id="Yes" value="true" autocomplete="off" checked>
            <label class="form-check-label" for="Yes">Yes</label>
        </div>
    </div>

    <div class="col-md-1">
        <div class="form-check">
            <input type="radio" class="form-check-input" name="Comparative" id="No" value="false" autocomplete="off">
            <label class="form-check-label" for="No">No</label>
        </div>
    </div>
