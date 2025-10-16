@{
    // Safely cast the dropdown to a list
    var finYears = ViewBag.FinYearDropdown as List<YourNamespace.Models.FinYearDD>;
    string currentFY = ViewBag.CurrentFY as string;
    var selectedFinYear = finYears?.FirstOrDefault(f => f.FinYear == currentFY);
}

<select class="form-control form-control-sm custom-select me-2" name="FinYearID" id="FinYearID" disabled>
    @foreach (var item in finYears)
    {
        var isSelected = (item.FinYear == currentFY);
        <option value="@item.Id" selected="@(isSelected ? "selected" : null)">
            @item.FinYear
        </option>
    }
</select>

<!-- Hidden field for form submission -->
@if (selectedFinYear != null)
{
    <input type="hidden" name="FinYearID" value="@selectedFinYear.Id" />
}




getting this 2 errors 

Cannot use a lambda expression as an argument to a dynamically dispatched operation without first casting it to a delegate or expression tree type.
The tag helper 'option' must not have C# in the element's attribute declaration area.
