<div id="monthlyYearlyFields" style="display:none;">
    <div class="row mt-3">
        <div class="col-md-4">
            <label>KPI SPOC Date & Time</label>
            <input type="datetime-local" class="form-control form-control-sm" id="KpiSPOC" name="KPISPOC_DateTime">
        </div>

        <div class="col-md-4">
            <label>Immediate Superior Date & Time</label>
            <input type="datetime-local" class="form-control form-control-sm" id="ImmediateSuperior" name="ImmediateSuperior_DateTime">
        </div>

        <div class="col-md-4">
            <label>HOD Date & Time</label>
            <input type="datetime-local" class="form-control form-control-sm" id="HOD" name="HOD_DateTime">
        </div>
    </div>
</div>

<div id="quarterlyFields" style="display:none;">
    <h6 class="mt-3 fw-bold">Quarterly Deadlines</h6>

    <div class="row mt-2">
        <div class="col-md-3">
            <label>Quarter 1</label>
            <input type="datetime-local" class="form-control form-control-sm" id="Quarter1" name="Quarter1_DateTime">
        </div>
        <div class="col-md-3">
            <label>Quarter 2</label>
            <input type="datetime-local" class="form-control form-control-sm" id="Quarter2" name="Quarter2_DateTime">
        </div>
        <div class="col-md-3">
            <label>Quarter 3</label>
            <input type="datetime-local" class="form-control form-control-sm" id="Quarter3" name="Quarter3_DateTime">
        </div>
        <div class="col-md-3">
            <label>Quarter 4</label>
            <input type="datetime-local" class="form-control form-control-sm" id="Quarter4" name="Quarter4_DateTime">
        </div>
    </div>
</div>




public class AppPeriodicityMaster
{

    public Guid ID { get; set; }
    public string? PeriodicityCode { get; set; }
    public string? PeriodicityName { get; set; }
    public string? CreatedBy { get; set; }
    public string? KPISPOC { get; set; }
    public string? ImmediateSuperior { get; set; }
    public string? HOD { get; set; }
    public string? Category { get; set; }
    public DateTime? CreatedOn { get; set; }
}
