[HttpGet]
public async Task<IActionResult> GetTargetDetails(Guid kpiId)
{
    var sql = @"
        SELECT 
            d.PeriodicityTransactionID,
            d.TargetValue
        FROM App_TargetSettingDetails_NOPR d
        INNER JOIN App_TargetSetting_NOPR ts 
            ON d.TargetSettingID = ts.ID
        WHERE ts.KPIID = @KPIID
    ";

    var parameters = new[]
    {
        new SqlParameter("@KPIID", kpiId)
    };

    var data = await context
        .App_TargetSettingDetails_NOPR
        .FromSqlRaw(sql, parameters)
        .Select(d => new
        {
            d.PeriodicityTransactionID,
            d.TargetValue
        })
        .ToListAsync();

    return Json(data);
}


try {
    const response = await fetch(`/TPR/GetPeriodicity?periodicityId=${periodicityId}`);
    const periods = await response.json();

    if (periods.length > 0) {
        periodicityContainer.innerHTML = "";
        const total = periods.length;

        // 1️⃣ Fetch existing target values
        const kpiId = this.dataset.id;
        const targetResponse = await fetch(`/TPR/GetTargetDetails?kpiId=${kpiId}`);
        const targetData = await targetResponse.json();

        // Convert targetData into a lookup object for easy access
        const targetMap = {};
        targetData.forEach(t => {
            targetMap[t.PeriodicityTransactionID] = t.TargetValue;
        });

        // 2️⃣ Create the periodicity input boxes
        periods.forEach((period, index) => {
            let colClass = "col-md-3"; 
            if (total <= 4) colClass = "col-md-6"; 
            else if (total === 1) colClass = "col-12"; 
            else if (total <= 6) colClass = "col-md-4";

            const existingValue = targetMap[period] || "";

            const div = document.createElement("div");
            div.className = `${colClass} mb-2`;

            div.innerHTML = `
                <div class="input-group input-group-sm flex-nowrap">
                    <span class="input-group-text text-truncate" 
                          style="max-width: 200px;" 
                          title="${period}">
                        ${period}
                    </span>

                    <input type="hidden" 
                           name="TargetDetails[${index}].PeriodicityTransactionID" 
                           value="${period}" />

                    <input type="text" class="form-control" 
                           name="TargetDetails[${index}].TargetValue" 
                           placeholder="Target" 
                           autocomplete="off"
                           value="${existingValue}">
                </div>
            `;
            periodicityContainer.appendChild(div);
        });

    } else {
        periodicityContainer.innerHTML = `<div class="text-danger small">No periods found for this KPI.</div>`;
    }

} catch (error) {
    periodicityContainer.innerHTML = `<div class="text-danger small">Error loading periodicity data.</div>`;
    console.error(error);
}




this is my query 

SELECT distinct
    k.ID,
    ts.*,
    k.KPIDetails,
    u.UnitCode,
    k.UnitID,
    p.PeriodicityName,
    k.KPICode,
    k.Department,
    k.Division,
    k.Section,
    k.PeriodicityID,
    k.UnitID
FROM App_KPIMaster_NOPR k
LEFT JOIN App_PeriodicityMaster_NOPR p ON k.PeriodicityID = p.ID
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
LEFT JOIN App_Prespectives_NOPR ps ON k.PerspectiveID = ps.ID
LEFT JOIN App_TypeofKPI_NOPR t ON k.TypeofKPIID = t.ID
LEFT JOIN App_GoodPerformance_NOPR g ON k.GoodPerformance = g.ID
LEFT JOIN App_TargetSetting_NOPR ts On K.ID = ts.KPIID
WHERE
    (@search IS NULL OR k.KPICode LIKE '%' + @search + '%' OR u.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%')
AND (@search3 IS NULL OR k.KPIDetails LIKE '%' + @search3 + '%')
ORDER BY k.KPIDetails
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY



and I have this table App_TargetSettingDetails_NOPR which contains details of target , I want to fetch the details from that and shows in the textboxes of target 

<script>
document.addEventListener('DOMContentLoaded', function () {
    const KPIMaster = document.getElementById("form");
    const refNoLinks = document.querySelectorAll(".refNoLink");
    const deleteButton = document.getElementById("deleteButton");
    const submitButton = document.getElementById("submitButton");
    const actionTypeInput = document.getElementById("actionType");


    refNoLinks.forEach(link => {
        link.addEventListener("click", async function (event) {
            event.preventDefault();
            KPIMaster.style.display = "block";

            document.getElementById("KPICode").value = this.dataset.kpicode;
            document.getElementById("UnitCode").value = this.dataset.unitcode;
            document.getElementById("KPIDetails").value = this.dataset.kpidetails;
            document.getElementById("KPIID").value = this.dataset.id;
            document.getElementById("PeriodicityID").value = this.dataset.periodicityid;
            document.getElementById("Relevant_comparative_available").value = this.dataset.relevant_comparative_available;
            document.getElementById("Relevant_comparative_available_Value").value = this.dataset.relevant_comparative_available_value;
            document.getElementById("Relevant_comparative_available_Details").value = this.dataset.relevant_comparative_available_details;
            document.getElementById("Current_performance_better_than_comparative").value = this.dataset.current_performance_better_than_comparative;
            document.getElementById("Current_performance_better_than_comparative_Value").value = this.dataset.current_performance_better_than_comparative_value;
            document.getElementById("Current_performance_better_than_comparative_Details").value = this.dataset.current_performance_better_than_comparative_details;
            document.getElementById("Theoretical_limit_known").value = this.dataset.theoretical_limit_known;
            document.getElementById("Theoretical_limit_known_Value").value = this.dataset.theoretical_limit_known_value;
            document.getElementById("Theoretical_limit_known_Details").value = this.dataset.theoretical_limit_known_details;
            document.getElementById("Statutory_standard_guidline_known").value = this.dataset.statutory_standard_guidline_known;
            document.getElementById("Statutory_standard_guidline_known_Value").value = this.dataset.statutory_standard_guidline_known_value;
            document.getElementById("Statutory_standard_guidline_known_Details").value = this.dataset.statutory_standard_guidline_known_details;
            document.getElementById("Internal_Benchmark_available").value = this.dataset.internal_benchmark_available;
            document.getElementById("Internal_Benchmark_available_Value").value = this.dataset.internal_benchmark_available_value;
            document.getElementById("Internal_Benchmark_available_Details").value = this.dataset.internal_benchmark_available_details;
            document.getElementById("hist1").value = this.dataset.historical_bast_available;
            document.getElementById("hist2").value = this.dataset.historical_bast_available_value;
            document.getElementById("hist3").value = this.dataset.historical_bast_available_details;

            if (deleteButton) deleteButton.style.display = "inline-block";

            const periodicityId = this.dataset.periodicityid;
            const periodicityContainer = document.getElementById("periodicityFields");

            periodicityContainer.innerHTML = `
                <div class='text-muted small'>Loading periods...</div>
            `;

            try {
                const response = await fetch(`/TPR/GetPeriodicity?periodicityId=${periodicityId}`);
                const periods = await response.json();

                if (periods.length > 0) {
                    periodicityContainer.innerHTML = "";
                    const total = periods.length;

                    periods.forEach((period, index) => {
                        let colClass = "col-md-3"; 
                        if (total <= 4) colClass = "col-md-6"; 
                        else if (total === 1) colClass = "col-12"; 
                        else if (total <= 6) colClass = "col-md-4";

                        const div = document.createElement("div");
                        div.className = `${colClass} mb-2`;

                        div.innerHTML = `
                            <div class="input-group input-group-sm flex-nowrap">
                                <span class="input-group-text text-truncate" 
                                      style="max-width: 200px;" 
                                      title="${period}">
                                    ${period}
                                </span>

                                <input type="hidden" 
                                       name="TargetDetails[${index}].PeriodicityTransactionID" 
                                       value="${period}" />

                                <input type="text" class="form-control" 
                                       name="TargetDetails[${index}].TargetValue" 
                                       placeholder="Target" autocomplete="off">
                            </div>
                        `;
                        periodicityContainer.appendChild(div);
                    });
                } else {
                    periodicityContainer.innerHTML = `<div class="text-danger small">No periods found for this KPI.</div>`;
                }

            } catch (error) {
                periodicityContainer.innerHTML = `<div class="text-danger small">Error loading periodicity data.</div>`;
                console.error(error);
            }
        });
    });

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
                    document.getElementById("form").submit();
                }
            });
        });
    }
});
</script>

and this is my anchor tag
   <a href="#"
   class="refNoLink"
   data-id="@item.ID"
   data-KPICode="@item.KPICode"
   data-UnitCode="@item.UnitCode"
   data-KPIDetails="@item.KPIDetails"
   data-PeriodicityID="@item.PeriodicityID"
   data-Relevant_comparative_available="@item.Relevant_comparative_available"
   data-Relevant_comparative_available_Value="@item.Relevant_comparative_available_Value"
   data-Relevant_comparative_available_Details="@item.Relevant_comparative_available_Details"
   data-Current_performance_better_than_comparative="@item.Current_performance_better_than_comparative"
   data-Current_performance_better_than_comparative_Value="@item.Current_performance_better_than_comparative_Value"
   data-Current_performance_better_than_comparative_Details="@item.Current_performance_better_than_comparative_Details"
   data-Theoretical_limit_known="@item.Theoretical_limit_known"
   data-Theoretical_limit_known_Value="@item.Theoretical_limit_known_Value"
   data-Theoretical_limit_known_Details="@item.Theoretical_limit_known_Details"
   data-Statutory_standard_guidline_known="@item.Statutory_standard_guidline_known"
   data-Statutory_standard_guidline_known_Value="@item.Statutory_standard_guidline_known_Value"
   data-Statutory_standard_guidline_known_Details="@item.Statutory_standard_guidline_known_Details"
   data-Current_performance_better_than_statutory_standard="@item.Current_performance_better_than_statutory_standard"
   data-Current_performance_better_than_statutory_standard_Value="@item.Current_performance_better_than_statutory_standard_Value"
   data-Current_performance_better_than_statutory_standard_Details="@item.Current_performance_better_than_statutory_standard_Details"
   data-Internal_Benchmark_available="@item.Internal_Benchmark_available"
   data-Internal_Benchmark_available_Value="@item.Internal_Benchmark_available_Value"
   data-Internal_Benchmark_available_Details="@item.Internal_Benchmark_available_Details"
   data-Historical_bast_available="@item.Historical_bast_available"
   data-Historical_bast_available_Value="@item.Historical_bast_available_Value"
   data-Historical_bast_available_Details="@item.Historical_bast_available_Details"
   data-PeriodicityName="@item.PeriodicityName">
   @item.KPIDetails
</a>
