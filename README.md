only this 2 columns are not fetching the data PeriodicityName and TypeOfKPI 
<div class="col-md-3">

<select asp-for="PeriodicityID" class="form-control form-control-sm custom-select" name="PeriodicityName">
<option value=""></option>
@foreach (var item in PeriodicityDropdown)
{
				<option value="@item.ID">@item.PeriodicityCode</option>
}

</select>

 <div class="col-md-3">
<select asp-for="TypeofKPIID" class="form-control form-control-sm custom-select" name="TypeofKPIID">
<option value=""></option>
@foreach (var item in KPIDropdown)
{
				<option value="@item.ID">@item.TypeofKPI</option>
}

</select>
</div>


public class KpiListDto
{
    public Guid ID { get; set; }
    public string? KPIDetails { get; set; }       // KPI
    public string? Section { get; set; }
    public string? Company { get; set; }
    public string? Perspectives { get; set; }     // Area
    public string? UnitCode { get; set; }         // UOM
    public string? KPILevel { get; set; }
    public string? PeriodicityName { get; set; }
    public string? Division { get; set; }
    public string? Department { get; set; }
    public string? Name { get; set; }             // Good Performance Name
    public string? KPICode { get; set; }

    public string? KPIDefination { get; set; }

    public int? NoofDecimal { get; set; }

    public string? TypeOfKPI { get; set; }

  
    public Guid UnitID { get; set; }
    public string? CreatedBy { get; set; }
    public Guid? PeriodicityID { get; set; }
    public string? GoodPerformance { get; set; }
    public decimal? HistoricalBest { get; set; }
    public Guid? HistoricalBestYear { get; set; }
    public decimal? TheoreticalBest { get; set; }
  
    public Guid? PerspectiveID { get; set; }
    public Guid? TypeofKPIID { get; set; }
    public Guid? LTPSTPID { get; set; }
    public string? Central_Local { get; set; }
    public string? KPIUpto { get; set; }
    public bool? Deactivate { get; set; }
    public DateTime? DeactivateOn { get; set; }
  
    public string? KPIMode { get; set; }
    public string? SourceData { get; set; }
    public string? COMode { get; set; }
    public string? PCNCode { get; set; }
}


   <a href="#"
   class="refNoLink"
   data-id="@item.ID"
   data-KPIDetails="@item.KPIDetails"
   data-KPIDefination="@item.KPIDefination"
   data-KPILevel="@item.KPILevel"
   data-Perspectives="@item.Perspectives"
   data-UnitCode="@item.UnitCode"
   data-PeriodicityName="@item.PeriodicityName"
   data-Division="@item.Division"
   data-Department="@item.Department"
   data-Section="@item.Section"
   data-GoodPerformance="@item.Name"
   data-KPICode="@item.KPICode"
   data-NoofDecimal="@item.NoofDecimal"
   data-Company="@item.Company"
   data-PeriodicityName="@item.PeriodicityName">
   @item.KPIDetails
</a>


     refNoLinks.forEach(link => {
    link.addEventListener("click", function (event) {
        event.preventDefault();

        // Show the KPI form
        KPIMaster.style.display = "block";

        // Populate all fields
        setValue("KPIID", this.getAttribute("data-id"));
        setValue("KPICode", this.getAttribute("data-KPICode"));
        setValue("KPIDetails", this.getAttribute("data-KPIDetails"));
        setValue("KPIDefination", this.getAttribute("data-KPIDefination"));
        setValue("KPILevel", this.getAttribute("data-KPILevel"));
        setValue("Division", this.getAttribute("data-Division"));
        setValue("Department", this.getAttribute("data-Department"));
        setValue("Section", this.getAttribute("data-Section"));
        setValue("NoofDecimal", this.getAttribute("data-NoofDecimal"));
        setValue("PeriodicityName", this.getAttribute("data-PeriodicityName"));

        // For dropdowns — match by visible text
        selectDropdownByText("PerspectiveID", this.getAttribute("data-Perspectives"));
        selectDropdownByText("UnitID", this.getAttribute("data-UnitCode"));
        selectDropdownByText("PeriodicityName", this.getAttribute("data-PeriodicityName"));
        selectDropdownByText("GoodPerformance", this.getAttribute("data-GoodPerformance"));
        selectDropdownByText("TypeofKPIID", this.getAttribute("data-TypeofKPIID"));
        selectDropdownByText("Company", this.getAttribute("data-Company"));

        // Show delete button if exists
        if (deleteButton) {
            deleteButton.style.display = "inline-block";
        }

        // Optional: scroll to form
        KPIMaster.scrollIntoView({ behavior: "smooth" });
    });
});
