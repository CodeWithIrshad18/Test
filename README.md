document.addEventListener('DOMContentLoaded', function () {
    const searchInput = document.getElementById('worksiteSearch');
    const listItems = document.querySelectorAll('#locationList li');
    const noResultsMsg = document.getElementById('noResultsMsg');
    const dropdownToggle = document.getElementById('worksiteDropdown');

    if (searchInput) {
        searchInput.addEventListener('keyup', function () {
            const searchValue = this.value.toLowerCase();
            let matchCount = 0;

            listItems.forEach(li => {
                const label = li.querySelector('.form-check-label');
                if (!label) return; // Skip search input & "No results" message

                const text = label.textContent.toLowerCase();
                const match = text.includes(searchValue);
                li.style.display = match ? '' : 'none';
                if (match) matchCount++;
            });

            // Show or hide "No results found"
            noResultsMsg.style.display = matchCount === 0 ? 'block' : 'none';
        });
    }

    // 🧹 Reset search and show all items when dropdown closes
    if (dropdownToggle) {
        dropdownToggle.addEventListener('hidden.bs.dropdown', function () {
            searchInput.value = '';                // Clear search box
            noResultsMsg.style.display = 'none';   // Hide "No results" message

            listItems.forEach(li => {
                const label = li.querySelector('.form-check-label');
                if (label) li.style.display = '';  // Show all items again
            });
        });
    }
});




document.addEventListener('DOMContentLoaded', function () {
    const dropdown = document.getElementById('worksiteDropdown');
    const searchInput = document.getElementById('worksiteSearch');
    const listItems = document.querySelectorAll('#locationList li');
    const noResultsMsg = document.getElementById('noResultsMsg');

    if (!dropdown || !searchInput || !noResultsMsg) return;

    // 🔍 Live search filter
    searchInput.addEventListener('keyup', function () {
        const searchValue = this.value.toLowerCase();
        let matchCount = 0;

        listItems.forEach(li => {
            const label = li.querySelector('.form-check-label');
            if (!label) return; // skip search input and noResultsMsg

            const text = label.textContent.toLowerCase();
            const match = text.includes(searchValue);
            li.style.display = match ? '' : 'none';
            if (match) matchCount++;
        });

        // show or hide 'no results found'
        noResultsMsg.style.display = matchCount === 0 ? 'block' : 'none';
    });

    // 🧹 Reset search when dropdown is hidden (after it fully closes)
    dropdown.addEventListener('hidden.bs.dropdown', function () {
        searchInput.value = '';
        noResultsMsg.style.display = 'none';

        listItems.forEach(li => {
            const label = li.querySelector('.form-check-label');
            if (label) li.style.display = ''; // show all checkboxes again
        });
    });
});





<div class="col-md-4">
    <label>Worksite</label>

    <div class="dropdown">
        <input class="form-control form-control-sm" placeholder="Select Worksites"
               type="button" id="worksiteDropdown" data-bs-toggle="dropdown" aria-expanded="false" />

        <ul class="dropdown-menu w-100" aria-labelledby="worksiteDropdown" id="locationList" style="max-height: 200px; overflow-y: auto;">

            <!-- 🔍 Search box -->
            <li class="px-2">
                <input type="text" id="worksiteSearch" class="form-control form-control-sm mt-1 mb-2"
                       placeholder="Search worksite..." autocomplete="off" />
            </li>

            <!-- ⚠️ No results message -->
            <li id="noResultsMsg" class="text-center text-muted small" style="display: none;">
                No results found
            </li>

            <!-- ✅ Worksite list -->
            @foreach (var item in ViewBag.WorksiteList as List<SelectListItem>)
            {
                <li style="margin-left:5%;">
                    <div class="form-check">
                        <input type="checkbox" class="form-check-input worksite-checkbox"
                               value="@item.Value" id="worksite_@item.Value" />
                        <label class="form-check-label" for="worksite_@item.Value">@item.Text</label>
                    </div>
                </li>
            }
        </ul>
    </div>

    <input type="hidden" id="Worksite" name="Worksite" />
</div>

document.addEventListener('DOMContentLoaded', function () {
    const dropdown = document.getElementById('worksiteDropdown');
    const searchInput = document.getElementById('worksiteSearch');
    const listItems = document.querySelectorAll('#locationList li');
    const noResultsMsg = document.getElementById('noResultsMsg');

    if (!dropdown || !searchInput || !noResultsMsg) return;

    // 🔍 Live search filter
    searchInput.addEventListener('keyup', function () {
        const searchValue = this.value.toLowerCase();
        let matchCount = 0;

        listItems.forEach(li => {
            const label = li.querySelector('.form-check-label');
            if (!label) return; // skip search and noResultsMsg items

            const text = label.textContent.toLowerCase();
            const match = text.includes(searchValue);
            li.style.display = match ? '' : 'none';
            if (match) matchCount++;
        });

        noResultsMsg.style.display = matchCount === 0 ? 'block' : 'none';
    });

    // 🧹 Clear search input when dropdown closes
    dropdown.addEventListener('hide.bs.dropdown', function () {
        searchInput.value = '';
        noResultsMsg.style.display = 'none';
        listItems.forEach(li => (li.style.display = ''));
    });
});





<div class="col-md-4">
				<label>Worksite</label>

				<div class="dropdown">
					<input class="form-control form-control-sm" placeholder="Select Worksites"
						   type="button" id="worksiteDropdown" data-bs-toggle="dropdown" aria-expanded="false" />

					<ul class="dropdown-menu w-100" aria-labelledby="worksiteDropdown" id="locationList" style="max-height: 200px; overflow-y: auto;">
						@foreach (var item in ViewBag.WorksiteList as List<SelectListItem>)
						{
							<li style="margin-left:5%;">
								<div class="form-check">
									<input type="checkbox" class="form-check-input worksite-checkbox"
										   value="@item.Value" id="worksite_@item.Value" />
									<label class="form-check-label" for="worksite_@item.Value">@item.Text</label>
								</div>
							</li>
						}
					</ul>
				</div>

				<input type="hidden" id="Worksite" name="Worksite" />

</div>
