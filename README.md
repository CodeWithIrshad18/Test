    <form asp-action="PeriodicityMaster" asp-controller="Master" id="form" method="post" style="display:none;">
        <div class="card card-custom mt-4">
            <div class="card-header-custom">Periodicity Master</div>
            <div class="card-body">
                <div class="row g-3">
                    <input type="hidden" asp-for="ID" id="PeriodicityId" name="ID" />
                    <input type="hidden" id="actionType" name="actionType" />

                    <div class="col-md-4">
                        <label for="PeriodicityCode" class="control-label">Periodicity Code</label>
                        <input asp-for="PeriodicityCode" class="form-control form-control-sm" id="PeriodicityCode" autocomplete="off">
                    </div>

                    <div class="col-md-4">
                        <label for="PeriodicityName" class="control-label">Reporting Periodicity</label>
                        <input asp-for="PeriodicityName" class="form-control form-control-sm" id="PeriodicityName" autocomplete="off">
                    </div>

                    <div class="col-md-4">
                        <label for="MaximumEntryDate" class="control-label">Last Reporting Date</label>
                        <input asp-for="MaximumEntryDate" class="form-control form-control-sm" id="MaximumEntryDate" autocomplete="off">
                    </div>
                </div>

                <div class="text-center mt-4">
                    @if (ViewBag.CanModify == true || ViewBag.CanWrite == true)
                    {
                        <button class="btn btn-primary me-2 px-4" id="submitButton" type="submit">Submit</button>
                    }
                    @if (ViewBag.CanDelete == true)
                    {
                        <button class="btn btn-danger px-4" id="deleteButton" style="display:none;">Delete</button>
                    }
                </div>
            </div>
        </div>
    </form>

CREATE TABLE [dbo].[App_PeriodicityMaster_NOPR] (
    [ID]               UNIQUEIDENTIFIER DEFAULT (newid()) NOT NULL,
    [PeriodicityCode]  VARCHAR (50)     NOT NULL,
    [PeriodicityName]  VARCHAR (50)     NOT NULL,
    [CreatedBy]        VARCHAR (50)     NULL,
    [MaximumEntryDate] VARCHAR (50)     NULL,
    CONSTRAINT [PK_App_PeriodicityMaster_NOPR] PRIMARY KEY CLUSTERED ([ID] ASC)
);
