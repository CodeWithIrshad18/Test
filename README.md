now I have this js to fetch the details 

refNoLinks.forEach(link => {
    link.addEventListener("click", function (event) {
        event.preventDefault();
        KPIMaster.style.display = "block";

        document.getElementById("KPICode").value = this.getAttribute("data-KPICode");
        document.getElementById("KPILevel").value = this.getAttribute("data-KPILevel");
        document.getElementById("Company").value = this.getAttribute("data-Company");
        document.getElementById("Division").value = this.getAttribute("data-Division");
        document.getElementById("Department").value = this.getAttribute("data-Department");
        document.getElementById("Section").value = this.getAttribute("data-Section");
        document.getElementById("PerspectiveID").value = this.getAttribute("data-PerspectiveID");
        document.getElementById("UnitID").value = this.getAttribute("data-UnitID");
        document.getElementById("KPIDefination").value = this.getAttribute("data-KPIDefination");
        document.getElementById("KPIDetails").value = this.getAttribute("data-KPIDetails");
        document.getElementById("PeriodicityID").value = this.getAttribute("data-PeriodicityID");
        document.getElementById("GoodPerformance").value = this.getAttribute("data-GoodPerformance");
        document.getElementById("NoofDecimal").value = this.getAttribute("data-NoofDecimal");
        document.getElementById("TypeofKPIID").value = this.getAttribute("data-TypeofKPIID");
        document.getElementById("KPIID").value = this.getAttribute("data-id");

        if (deleteButton) {
            deleteButton.style.display = "inline-block";
        }
    });
});

                    <td>
    <a href="#" 
       class="refNoLink"
       data-id="@item.ID"
       data-KPIDetails="@item.KPIDetails"
       data-KPILevel="@item.KPILevel"
       data-Perspectives="@item.Perspectives"
       data-UnitCode="@item.UnitCode"
       data-PeriodicityName="@item.PeriodicityName"
       data-Division="@item.Division"
       data-Department="@item.Department"
       data-GoodPerformance="@item.Name"
       data-KPICode="@item.KPICode">
       @item.KPIDetails
    </a>
</td>


but It is not working 
