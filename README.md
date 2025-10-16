this is my query to fetch the data              
   using (var connection = new SqlConnection(GetSAPConnectionString()))
                {
                    string sqlQuery = @"
        SELECT 
    k.ID,
    k.KPIDetails,
    ps.Perspectives,
    u.UnitCode,
    k.KPILevel,
    p.PeriodicityName,
    k.Division,
    k.Department,
    k.Section,
    g.Name,
    k.KPICode,
    k.TypeofKPIID,
    k.KPIDefination,
    k.Company,
    k.GoodPerformance,
    k.PeriodicityID,
    k.UnitID,
    k.PerspectiveID,
    k.NoofDecimal
FROM App_KPIMaster_NOPR k
LEFT JOIN App_PeriodicityMaster_NOPR p ON k.PeriodicityID = p.ID
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
LEFT JOIN App_Prespectives_NOPR ps ON k.PerspectiveID = ps.ID
LEFT JOIN App_TypeofKPI_NOPR t ON k.TypeofKPIID = t.ID
LEFT JOIN App_GoodPerformance_NOPR g ON k.GoodPerformance = g.ID
WHERE
    (@search IS NULL OR k.KPICode LIKE '%' + @search + '%' OR u.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%')
AND (@search3 IS NULL OR k.KPIDetails LIKE '%' + @search3 + '%')
ORDER BY k.KPIDetails
OFFSET @skip ROWS FETCH NEXT @take ROWS ONLY;
    ";

                    string countQuery = @"
        SELECT COUNT(*) 
FROM App_KPIMaster_NOPR k
LEFT JOIN App_UOM_NOPR u ON k.UnitID = u.ID
WHERE
    (@search IS NULL OR k.KPICode LIKE '%' + @search + '%' OR u.UnitCode LIKE '%' + @search + '%')
AND (@search2 IS NULL OR k.Department LIKE '%' + @search2 + '%')
AND (@search3 IS NULL OR k.KPIDetails LIKE '%' + @search3 + '%');

    ";

                    var pagedData = await connection.QueryAsync<KpiListDto>(sqlQuery, new
                    {
                        search = string.IsNullOrEmpty(searchString) ? null : searchString,
                        search2 = string.IsNullOrEmpty(Dept) ? null : Dept,
                        search3 = string.IsNullOrEmpty(KPI) ? null : KPI,
                        skip,
                        take = pageSize
                    });

                    var totalCount = await connection.ExecuteScalarAsync<int>(countQuery, new
                    {
                        search = string.IsNullOrEmpty(searchString) ? null : searchString,
                        search2 = string.IsNullOrEmpty(Dept) ? null : Dept,
                        search3 = string.IsNullOrEmpty(KPI) ? null : KPI
                    });

                    ViewBag.ListData2 = pagedData.ToList();
                    ViewBag.CurrentPage = page;
                    ViewBag.TotalPages = (int)Math.Ceiling(totalCount / (double)pageSize);
                    ViewBag.searchString = searchString;
                    ViewBag.Dept = Dept;
                    ViewBag.KPI = KPI;
                }

this is my anchor tag

   <a href="#"
   class="refNoLink"
   data-id="@item.ID"
   data-KPICode="@item.KPICode"
   data-KPILevel="@item.KPILevel"
   data-Company="@item.Company"
   data-Division="@item.Division"
   data-Department="@item.Department"
   data-Section="@item.Section"
   data-Section="@item.Section"
   data-PerspectiveID="@item.PerspectiveID"
   data-TypeofKPIID="@item.TypeofKPIID"
   data-UnitID="@item.UnitID"
   data-KPIDefination="@item.KPIDefination"
   data-KPIDetails="@item.KPIDetails"
   data-PeriodicityName="@item.PeriodicityID"
   data-GoodPerformance="@item.GoodPerformance"
   data-NoofDecimal="@item.NoofDecimal"
data-Company="@item.Company"">
   @item.KPIDetails
</a>

and this is my js 

 refNoLinks.forEach(link => {
     link.addEventListener("click", function (event) {
         event.preventDefault();
         KPIMaster.style.display = "block";


        document.getElementById("KPICode").value = this.getAttribute("data-KPICode");
         document.getElementById("KPILevel").value = this.getAttribute("data-KPILevel");
         document.getElementById("Company").value = this.getAttribute("data-Company");
         document.getElementById("PerspectiveID").value = this.getAttribute("data-PerspectiveID");
         document.getElementById("TypeofKPIID").value = this.getAttribute("data-TypeofKPIID");
         document.getElementById("UnitID").value = this.getAttribute("data-UnitID");
         document.getElementById("KPIDefination").value = this.getAttribute("data-KPIDefination");
         document.getElementById("KPIDetails").value = this.getAttribute("data-KPIDetails");
         document.getElementById("PeriodicityID").value = this.getAttribute("data-PeriodicityName");
         document.getElementById("GoodPerformance").value = this.getAttribute("data-GoodPerformance");
         document.getElementById("NoofDecimal").value = this.getAttribute("data-NoofDecimal");
         document.getElementById("KPIID").value = this.getAttribute("data-id");

         

         if (deleteButton) deleteButton.style.display = "inline-block";
     });
 });

why data is not populate on this three textboxes, KPICode is populates but 2 are not 
 <input asp-for="KPIDetails" class="form-control form-control-sm" autocomplete="off" id="KPIDetails" name="KPIDetails" style="height:50%;" readonly/>
<input asp-for="UnitID"  class="form-control form-control-sm col-sm-2" id="UnitID" name="UnitID" autocomplete="off" readonly>
<input class="form-control form-control-sm" id="KPICode" autocomplete="off" readonly>
