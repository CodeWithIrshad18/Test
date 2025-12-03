<script>
document.addEventListener('DOMContentLoaded', function () {

    const KPIMaster = document.getElementById("form");
    const refNoLinks = document.querySelectorAll(".refNoLink");
    const deleteButton = document.getElementById("deleteButton");
    const submitButton = document.getElementById("submitButton");
    const actionTypeInput = document.getElementById("actionType");
    const periodSelect = document.getElementById("Period");
    const targetInput = document.getElementById("Target");

    /* HOLD section elements (MAY NOT EXIST for non-admin) */
    const holdYes = document.getElementById("YES");
    const holdNo = document.getElementById("NO");
    const holdReasonField = document.querySelectorAll(".deactive-field");
    const holdReasonInput = document.getElementById("HoldReason");
    const holdSection = document.getElementById("HoldSection");

    const valueSection = document.getElementById("ValueSection");
    const ytdValueSection = document.getElementById("YTDValueSection");
    const YTDValueSectionlabel = document.getElementById("YTDValueSectionlabel");
    const ValueSectionlabel = document.getElementById("ValueSectionlabel");

    function show(el) {
        if (el) {
            el.classList.remove("hidden");
            el.classList.add("visible");
        }
    }

    function hide(el) {
        if (el) {
            el.classList.remove("visible");
            el.classList.add("hidden");
        }
    }

    function toggleHoldFields() {
        if (!holdYes || !holdNo) return;

        if (holdYes.checked) {
            holdReasonField.forEach(el => show(el));
            hide(valueSection);
            hide(ytdValueSection);
            hide(YTDValueSectionlabel);
            hide(ValueSectionlabel);
        } else {
            holdReasonField.forEach(el => hide(el));
            if (holdReasonInput) holdReasonInput.value = "";

            show(valueSection);
            show(ytdValueSection);
            show(YTDValueSectionlabel);
            show(ValueSectionlabel);
        }
    }

    /* Safely assign holdYes/holdNo events only if exist (admin only) */
    if (holdYes && holdNo) {
        holdYes.addEventListener("change", toggleHoldFields);
        holdNo.addEventListener("change", toggleHoldFields);
    }

    /* Initial hide */
    if (holdSection) hide(holdSection);
    hide(valueSection);
    hide(ytdValueSection);
    hide(YTDValueSectionlabel);
    hide(ValueSectionlabel);

    if (holdReasonField && holdReasonField.length > 0) {
        holdReasonField.forEach(el => hide(el));
    }

    /* Handle reference links */
    refNoLinks.forEach(link => {
        link.addEventListener("click", async function (event) {
            event.preventDefault();
            KPIMaster.style.display = "block";

            if (holdNo) holdNo.checked = true;
            if (holdReasonInput) holdReasonInput.value = "";

            if (holdSection) hide(holdSection);
            hide(valueSection);
            hide(ytdValueSection);
            hide(YTDValueSectionlabel);
            hide(ValueSectionlabel);

            if (holdReasonField) {
                holdReasonField.forEach(el => hide(el));
            }

            document.getElementById("KPICode").value = this.dataset.kpicode;
            document.getElementById("Company").value = this.dataset.company;
            document.getElementById("Department").value = this.dataset.department;
            document.getElementById("Division").value = this.dataset.division;
            document.getElementById("Section").value = this.dataset.section;
            document.getElementById("TypeofKPI").value = this.dataset.typeofkpi;
            document.getElementById("UnitCode").value = this.dataset.unitcode;
            document.getElementById("KPIDefination").value = this.dataset.kpidetails;
            document.getElementById("FinYear").value = this.dataset.finyear;
            document.getElementById("FinYearID").value = this.dataset.finyearid;
            document.getElementById("KPIID").value = this.dataset.kpiid;
            document.getElementById("PeriodicityID").value = this.dataset.periodicityname;

            const tsid = this.dataset.tsid;

            periodSelect.innerHTML = '<option value="">Select</option>';
            periodSelect.dataset.periodData = "[]";
            targetInput.value = "";
            document.getElementById("PeriodID").value = "";

            if (!tsid) return;

            try {
                const response = await fetch(`/TPR/GetTargets?TSID=${tsid}`);
                const data = await response.json();

                if (Array.isArray(data) && data.length > 0) {
                    data.forEach(item => {
                        const opt = document.createElement("option");
                        opt.value = item.ID;
                        opt.textContent = item.PeriodTransactionID || item.PeriodicityTransactionID || "(Unnamed Period)";
                        periodSelect.appendChild(opt);
                    });

                    periodSelect.dataset.periodData = JSON.stringify(data);
                }
            } catch (error) {
                console.error("Error fetching target details:", error);
            }

            if (deleteButton) deleteButton.style.display = "none";

            if (submitButton) {
                submitButton.addEventListener("click", function () {
                    actionTypeInput.value = "save";
                });
            }

            if (deleteButton) {
                deleteButton.addEventListener("click", function () {
                    Swal.fire({
                        title: 'Are you sure?',
                        text: "Do you really want to delete this Unit?",
                        icon: 'warning',
                        showCancelButton: true,
                        confirmButtonColor: '#3085d6',
                        cancelButtonColor: '#d33',
                        confirmButtonText: 'Yes, delete it!',
                        cancelButtonText: 'Cancel'
                    }).then((result) => {
                        if (result.isConfirmed) {
                            actionTypeInput.value = "delete";
                            KPIMaster.submit();
                        }
                    });
                });
            }
        });
    });

    /* When period is selected */
    periodSelect.addEventListener("change", function () {

        const selectedID = this.value;
        const periodData = this.dataset.periodData ? JSON.parse(this.dataset.periodData) : [];

        if (!selectedID) {

            if (holdNo) holdNo.checked = true;
            if (holdReasonInput) holdReasonInput.value = "";

            if (holdReasonField) {
                holdReasonField.forEach(el => hide(el));
            }

            if (holdSection) hide(holdSection);
            hide(valueSection);
            hide(ytdValueSection);
            hide(YTDValueSectionlabel);
            hide(ValueSectionlabel);
            return;
        }

        const typeofKPI = document.getElementById("TypeofKPI").value?.trim();

        /* Bonus KPI case */
        if (typeofKPI === "Bonus") {

            if (holdSection) hide(holdSection);

            if (holdYes) holdYes.disabled = true;
            if (holdNo) holdNo.disabled = true;
            if (holdReasonInput) holdReasonInput.disabled = true;

            if (holdReasonField) {
                holdReasonField.forEach(el => hide(el));
            }

            show(valueSection);
            show(ytdValueSection);
            show(YTDValueSectionlabel);
            show(ValueSectionlabel);

            const selectedItem = periodData.find(p => p.ID == selectedID);
            if (selectedItem) {
                targetInput.value = selectedItem.TargetValue || '';
                document.getElementById("PeriodID").value = selectedItem.ID;
                document.getElementById("Value").value = selectedItem.Value || '';
                document.getElementById("YTDValue").value = selectedItem.YTDValue || '';
            }

            return;
        }

        /* Normal KPI case (admin + non-admin) */
        if (holdSection) show(holdSection);
        show(valueSection);
        show(ytdValueSection);
        show(YTDValueSectionlabel);
        show(ValueSectionlabel);

        const selectedItem = periodData.find(p => p.ID == selectedID);
        if (!selectedItem) return;

        targetInput.value = selectedItem.TargetValue || '';
        document.getElementById("PeriodID").value = selectedItem.ID;
        document.getElementById("Value").value = selectedItem.Value || '';
        document.getElementById("YTDValue").value = selectedItem.YTDValue || '';

        /* Hold Logic */
        if (holdYes && holdNo) {

            if (selectedItem.Hold === true || selectedItem.Hold === "True" || selectedItem.Hold === 1) {
                holdYes.checked = true;
                if (holdReasonInput) holdReasonInput.value = selectedItem.HoldReason || "";
            } else {
                holdNo.checked = true;
                if (holdReasonInput) holdReasonInput.value = "";
            }

            toggleHoldFields();
        }
    });

});
</script>

    
    
    

<script>
    document.addEventListener('DOMContentLoaded', function () {
        const KPIMaster = document.getElementById("form");
        const refNoLinks = document.querySelectorAll(".refNoLink");
        const deleteButton = document.getElementById("deleteButton");
        const submitButton = document.getElementById("submitButton");
        const actionTypeInput = document.getElementById("actionType");
        const periodSelect = document.getElementById("Period");
        const targetInput = document.getElementById("Target");

        const holdYes = document.getElementById("YES");
        const holdNo = document.getElementById("NO");
        const holdReasonField = document.querySelectorAll(".deactive-field");
        const holdReasonInput = document.getElementById("HoldReason");

        const holdSection = document.getElementById("HoldSection"); 
        const valueSection = document.getElementById("ValueSection");
        const ytdValueSection = document.getElementById("YTDValueSection");
        const YTDValueSectionlabel = document.getElementById("YTDValueSectionlabel");
        const ValueSectionlabel = document.getElementById("ValueSectionlabel");


        function show(el) {
            el.classList.remove("hidden");
            el.classList.add("visible");
        }
        function hide(el) {
            el.classList.remove("visible");
            el.classList.add("hidden");
        }


        function toggleHoldFields() {
            if (holdYes.checked) {
                holdReasonField.forEach(el => show(el));
            

                hide(valueSection);
                hide(ytdValueSection);
                hide(YTDValueSectionlabel);
                hide(ValueSectionlabel);
            } else {
                holdReasonField.forEach(el => hide(el));
          
                holdReasonInput.value = "";

                show(valueSection);
                show(ytdValueSection);
                     show(YTDValueSectionlabel);
                show(ValueSectionlabel);
            }
        }

        holdYes.addEventListener("change", toggleHoldFields);
        holdNo.addEventListener("change", toggleHoldFields);


        hide(holdSection);
        hide(valueSection);
        hide(ytdValueSection);
             hide(YTDValueSectionlabel);
                hide(ValueSectionlabel);
        holdReasonField.forEach(el => hide(el));

        refNoLinks.forEach(link => {
            link.addEventListener("click", async function (event) {
                event.preventDefault();
                KPIMaster.style.display = "block";


                holdNo.checked = true;
                holdReasonInput.value = "";
                hide(holdSection);
                hide(valueSection);
                hide(ytdValueSection);
                 hide(YTDValueSectionlabel);
                hide(ValueSectionlabel);
                holdReasonField.forEach(el => hide(el));

                document.getElementById("KPICode").value = this.dataset.kpicode;
                document.getElementById("Company").value = this.dataset.company;
                document.getElementById("Department").value = this.dataset.department;
                document.getElementById("Division").value = this.dataset.division;
                document.getElementById("Section").value = this.dataset.section;
                document.getElementById("TypeofKPI").value = this.dataset.typeofkpi;
                document.getElementById("UnitCode").value = this.dataset.unitcode;
                document.getElementById("KPIDefination").value = this.dataset.kpidetails;
                document.getElementById("FinYear").value = this.dataset.finyear;
                document.getElementById("FinYearID").value = this.dataset.finyearid;
                document.getElementById("KPIID").value = this.dataset.kpiid;
                document.getElementById("PeriodicityID").value = this.dataset.periodicityname;

                const tsid = this.dataset.tsid;
                periodSelect.innerHTML = '<option value="">Select</option>';
                periodSelect.dataset.periodData = "[]";
                targetInput.value = "";
                document.getElementById("PeriodID").value = "";

                if (!tsid) return;

                try {
                    const response = await fetch(`/TPR/GetTargets?TSID=${tsid}`);
                    const data = await response.json();

                    if (!Array.isArray(data) || data.length === 0) {
                        console.warn("No target data found for TSID:", tsid);
                        return;
                    }

                    data.forEach(item => {
                        const opt = document.createElement("option");
                        opt.value = item.ID;
                        opt.textContent = item.PeriodTransactionID || item.PeriodicityTransactionID || "(Unnamed Period)";
                        periodSelect.appendChild(opt);
                    });

                    periodSelect.dataset.periodData = JSON.stringify(data);

                } catch (error) {
                    console.error("Error fetching target details:", error);
                }

                if (deleteButton) deleteButton.style.display = "none";

                if (submitButton) {
                    submitButton.addEventListener("click", function () {
                        actionTypeInput.value = "save";
                    });
                }

                if (deleteButton) {
                    deleteButton.addEventListener("click", function () {
                        Swal.fire({
                            title: 'Are you sure?',
                            text: "Do you really want to delete this Unit?",
                            icon: 'warning',
                            showCancelButton: true,
                            confirmButtonColor: '#3085d6',
                            cancelButtonColor: '#d33',
                            confirmButtonText: 'Yes, delete it!',
                            cancelButtonText: 'Cancel'
                        }).then((result) => {
                            if (result.isConfirmed) {
                                actionTypeInput.value = "delete";
                                KPIMaster.submit();
                            }
                        });
                    });
                }
            });
        });
       periodSelect.addEventListener("change", function () {
    const selectedID = this.value;
    const periodData = this.dataset.periodData ? JSON.parse(this.dataset.periodData) : [];

    if (!selectedID) {
        holdYes.checked = false;
        holdNo.checked = true;
        holdReasonInput.value = "";
        holdReasonField.forEach(el => hide(el));
        hide(holdSection);
        hide(valueSection);
        hide(ytdValueSection);
        hide(YTDValueSectionlabel);
        hide(ValueSectionlabel);
        return;
    }

    const typeofKPI = document.getElementById("TypeofKPI").value?.trim();

    if (typeofKPI === "Bonus") {
        hide(holdSection);
        holdYes.disabled = true;
        holdNo.disabled = true;
        holdReasonInput.disabled = true;
        holdReasonField.forEach(el => hide(el));

        show(valueSection);
        show(ytdValueSection);
        show(YTDValueSectionlabel);
        show(ValueSectionlabel);

        const selectedItem = periodData.find(p => p.ID === selectedID);
        if (selectedItem) {
            targetInput.value = selectedItem.TargetValue || '';
            document.getElementById("PeriodID").value = selectedItem.ID;
            document.getElementById("Value").value = selectedItem.Value || '';
            document.getElementById("YTDValue").value = selectedItem.YTDValue || '';
        }
        return; 
    }


    show(holdSection);
    show(valueSection);
    show(ytdValueSection);
    show(YTDValueSectionlabel);
    show(ValueSectionlabel);

    const selectedItem = periodData.find(p => p.ID === selectedID);
    if (!selectedItem) return;

    targetInput.value = selectedItem.TargetValue || '';
    document.getElementById("PeriodID").value = selectedItem.ID;
    document.getElementById("Value").value = selectedItem.Value || '';
    document.getElementById("YTDValue").value = selectedItem.YTDValue || '';

    if (selectedItem.Hold === true || selectedItem.Hold === "True" || selectedItem.Hold === 1) {
        holdYes.checked = true;
        holdReasonInput.value = selectedItem.HoldReason || "";
    } else {
        holdNo.checked = true;
        holdReasonInput.value = "";
    }

    toggleHoldFields();
});
    });
    </script>
error : 

Uncaught TypeError: Cannot read properties of null (reading 'addEventListener')
    at HTMLDocument.<anonymous> (ActualKPI:801:17)
