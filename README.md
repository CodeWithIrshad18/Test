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
