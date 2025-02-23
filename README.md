<div id="popup1" class="popup-container">
    <div class="popup-content">
        <a href="#" class="close"><i class="fas fa-window-close"></i></a>
        <div class="row d-flex justify-content-center">
            <div class="col-md-4 col-sm-6 col-12">
                <a asp-action="ViewerForm" asp-route-L2="L2 KPIs - Technical Services">
                    <div class="card l-bg-cherry">
                        @if (ViewBag.UnreadNotifications != null && ViewBag.UnreadNotifications.ContainsKey("L2 KPIs - Technical Services"))
                        {
                            <span class="badge rounded-pill badge-notification bg-danger position-absolute top-0 end-0 m-2">
                                @ViewBag.UnreadNotifications["L2 KPIs - Technical Services"]
                            </span>
                        }
                        <div class="card-statistic-3 p-4">
                            <div class="mb-4">
                                <h6 class="card-title mb-0">L2 KPIs</h6>
                            </div>
                            <div class="row align-items-center mb-2">
                                <div class="col-12">
                                    <h7 class="d-flex align-items-center mb-0 head">Technical Services</h7>
                                </div>
                            </div>
                        </div>
                    </div>
                </a>
            </div>
        </div>
        
        <div class="row">
            <div class="col-md-3 col-sm-6 col-12">
                <a asp-action="ViewerForm" asp-route-Admin="L3 KPIs - Admin & CC">
                    <div class="card l-bg-grey-dark">
                        @if (ViewBag.UnreadNotifications != null && ViewBag.UnreadNotifications.ContainsKey("L3 KPIs - Admin & CC"))
                        {
                            <span class="badge rounded-pill badge-notification bg-danger position-absolute top-0 end-0 m-2">
                                @ViewBag.UnreadNotifications["L3 KPIs - Admin & CC"]
                            </span>
                        }
                        <div class="card-statistic-3 p-4">
                            <div class="mb-4">
                                <h6 class="card-title mb-0">L3 KPIs</h6>
                            </div>
                            <div class="row align-items-center mb-2">
                                <div class="col-12">
                                    <h7 class="d-flex align-items-center mb-0 head">Admin & CC</h7>
                                </div>
                            </div>
                        </div>
                    </div>
                </a>
            </div>

            <div class="col-md-3 col-sm-6 col-12">
                <a asp-action="ViewerForm" asp-route-Bidding="L3 KPIs - Bidding">
                    <div class="card l-bg-blue-dark">
                        @if (ViewBag.UnreadNotifications != null && ViewBag.UnreadNotifications.ContainsKey("L3 KPIs - Bidding"))
                        {
                            <span class="badge rounded-pill badge-notification bg-danger position-absolute top-0 end-0 m-2">
                                @ViewBag.UnreadNotifications["L3 KPIs - Bidding"]
                            </span>
                        }
                        <div class="card-statistic-3 p-4">
                            <div class="mb-4">
                                <h6 class="card-title mb-0">L3 KPIs</h6>
                            </div>
                            <div class="row align-items-center mb-2">
                                <div class="col-12">
                                    <h7 class="d-flex align-items-center mb-0 head">Bidding</h7>
                                </div>
                            </div>
                        </div>
                    </div>
                </a>
            </div>

            <div class="col-md-3 col-sm-6 col-12">
                <a asp-action="ViewerForm" asp-route-BE="L3 KPIs - BE,Data Analytics,BD,CRM">
                    <div class="card l-bg-orange-dark">
                        @if (ViewBag.UnreadNotifications != null && ViewBag.UnreadNotifications.ContainsKey("L3 KPIs - BE,Data Analytics,BD,CRM"))
                        {
                            <span class="badge rounded-pill badge-notification bg-danger position-absolute top-0 end-0 m-2">
                                @ViewBag.UnreadNotifications["L3 KPIs - BE,Data Analytics,BD,CRM"]
                            </span>
                        }
                        <div class="card-statistic-3 p-4">
                            <div class="mb-4">
                                <h6 class="card-title mb-0">L3 KPIs</h6>
                            </div>
                            <div class="row align-items-center mb-2">
                                <div class="col-12">
                                    <h7 class="d-flex align-items-center mb-0 head">BE, Data Analytics, BD, CRM</h7>
                                </div>
                            </div>
                        </div>
                    </div>
                </a>
            </div>

            <div class="col-md-3 col-sm-6 col-12">
                <a asp-action="ViewerForm" asp-route-DETP="L3 KPIs - DETP">
                    <div class="card l-bg-green-dark">
                        @if (ViewBag.UnreadNotifications != null && ViewBag.UnreadNotifications.ContainsKey("L3 KPIs - DETP"))
                        {
                            <span class="badge rounded-pill badge-notification bg-danger position-absolute top-0 end-0 m-2">
                                @ViewBag.UnreadNotifications["L3 KPIs - DETP"]
                            </span>
                        }
                        <div class="card-statistic-3 p-4">
                            <div class="mb-4">
                                <h6 class="card-title mb-0">L3 KPIs</h6>
                            </div>
                            <div class="row align-items-center mb-2">
                                <div class="col-12">
                                    <h7 class="d-flex align-items-center mb-0 head">DETP</h7>
                                </div>
                            </div>
                        </div>
                    </div>
                </a>
            </div>
        </div>
    </div>
</div>

.popup-container {
    visibility: hidden;
    opacity: 0;
    transition: all 0.3s ease-in-out;
    transform: scale(1.1);
    position: fixed;
    z-index: 1000;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(21, 17, 17, 0.61);
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.popup-content {
    background-color: #fefefe;
    padding: 20px;
    border-radius: 10px;
    width: 90%;
    max-width: 800px;
    box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
    overflow-y: auto;
}

.close {
    position: absolute;
    top: 15px;
    right: 15px;
    font-size: 20px;
    color: #333;
    text-decoration: none;
}

.card {
    position: relative;
    border-radius: 10px;
    overflow: hidden;
    margin: 10px 0;
}

@media (max-width: 768px) {
    .popup-content {
        width: 95%;
        padding: 15px;
    }

    .row {
        flex-direction: column;
    }

    .col-md-3 {
        width: 100%;
    }

    .close {
        top: 10px;
        right: 10px;
        font-size: 18px;
    }
}




<div id="popup1" class="popup-container">
    <div class="popup-content">
        <a href="#" class="close"><i class="fas fa-window-close"></i></a>
        <div class="row col-md-12 d-flex justify-content-center">
            <div class="col-sm-4">
                <a asp-action="ViewerForm" asp-route-L2="L2 KPIs - Technical Services">
                    <div class="card l-bg-cherry">
                        @if (ViewBag.UnreadNotifications != null && ViewBag.UnreadNotifications.ContainsKey("L2 KPIs - Technical Services"))
                        {
                            <span class="badge rounded-pill badge-notification bg-danger position-absolute top-0 end-0 m-2">
                                @ViewBag.UnreadNotifications["L2 KPIs - Technical Services"]
                            </span>
                        }
                        <div class="card-statistic-3 p-4">

                            <div class="mb-4">
                                <h6 class="card-title mb-0" name="L2">
                                    L2 KPIs
                                </h6>
                            </div>
                            <div class="row align-items-center mb-2 d-flex">
                                <div class="col-12">
                                    <h7 class="d-flex align-items-center mb-0 head">
                                        Technical Services
                                    </h7>
                                </div>

                            </div>
                        </div>
                    </div>
                </a>
            </div>
            
           
        </div>
        <div class="row col-md-12">
            <div class="col-sm-3">
                <a asp-action="ViewerForm" asp-route-Admin="L3 KPIs - Admin & CC">
                    <div class="card l-bg-grey-dark">
                        @if (ViewBag.UnreadNotifications != null && ViewBag.UnreadNotifications.ContainsKey("L3 KPIs - Admin & CC"))
                        {
                            <span class="badge rounded-pill badge-notification bg-danger position-absolute top-0 end-0 m-2">
                                @ViewBag.UnreadNotifications["L3 KPIs - Admin & CC"]
                            </span>
                        }
                        <div class="card-statistic-3 p-4">

                            <div class="mb-4">
                                <h6 class="card-title mb-0">

                                    L3 KPIs

                                </h6>
                            </div>
                            <div class="row align-items-center mb-2 d-flex">
                                <div class="col-8">
                                    <h7 class="d-flex align-items-center mb-0 head">
                                        Admin & CC
                                    </h7>
                                </div>

                            </div>

                        </div>
                    </div>
                </a>
            </div>
            <div class="col-sm-3">
                <a asp-action="ViewerForm" asp-route-Bidding="L3 KPIs - Bidding">
                    <div class="card l-bg-blue-dark">
                        @if (ViewBag.UnreadNotifications != null && ViewBag.UnreadNotifications.ContainsKey("L3 KPIs - Bidding"))
                        {
                            <span class="badge rounded-pill badge-notification bg-danger position-absolute top-0 end-0 m-2">
                                @ViewBag.UnreadNotifications["L3 KPIs - Bidding"]
                            </span>
                        }
                        <div class="card-statistic-3 p-4">

                            <div class="mb-4">
                                <h6 class="card-title mb-0">

                                    L3 KPIs

                                </h6>
                            </div>
                            <div class="row align-items-center mb-2 d-flex">
                                <div class="col-12">
                                    <h7 class="d-flex align-items-center mb-0 head">
                                        Bidding
                                    </h7>
                                </div>
                            </div>
                        </div>
                    </div>
                </a>
            </div>
            <div class="col-sm-3">
                <a asp-action="ViewerForm" asp-route-BE="L3 KPIs - BE,Data Analytics,BD,CRM">
                    <div class="card l-bg-orange-dark">
                        @if (ViewBag.UnreadNotifications != null && ViewBag.UnreadNotifications.ContainsKey("L3 KPIs - BE,Data Analytics,BD,CRM"))
                        {
                            <span class="badge rounded-pill badge-notification bg-danger position-absolute top-0 end-0 m-2">
                                @ViewBag.UnreadNotifications["L3 KPIs - BE,Data Analytics,BD,CRM"]
                            </span>
                        }
                        <div class="card-statistic-3 p-4">

                            <div class="mb-4">
                                <h6 class="card-title mb-0">

                                    L3 KPIs

                                </h6>
                            </div>
                            <div class="row align-items-center mb-2 d-flex">
                                <div class="col-12">
                                    <h7 class="d-flex align-items-center mb-0 head">
                                        BE, Data Analytics, BD, CRM
                                    </h7>
                                </div>
                            </div>
                        </div>
                    </div>
                </a>
            </div>
            <div class="col-sm-3">
                <a asp-action="ViewerForm" asp-route-DETP="L3 KPIs - DETP">
                    <div class="card l-bg-green-dark">
                        @if (ViewBag.UnreadNotifications != null && ViewBag.UnreadNotifications.ContainsKey("L3 KPIs - DETP"))
                        {
                            <span class="badge rounded-pill badge-notification bg-danger position-absolute top-0 end-0 m-2">
                                @ViewBag.UnreadNotifications["L3 KPIs - DETP"]
                            </span>
                        }
                        <div class="card-statistic-3 p-4">

                            <div class="mb-4">
                                <h6 class="card-title mb-0">

                                    L3 KPIs

                                </h6>
                            </div>
                            <div class="row align-items-center mb-2 d-flex">
                                <div class="col-12">
                                    <h7 class="d-flex align-items-center mb-0 head">
                                        DETP
                                    </h7>
                                </div>

                            </div>

                        </div>
                    </div>
                </a>
            </div>
            
            
        </div>
        
       
       
    </div>
</div>

.popup-container {
    visibility: hidden;
    opacity: 0;
    transition: all 0.3s ease-in-out;
    transform: scale(1.3);
    position: fixed;
    z-index: 1;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(21, 17, 17, 0.61);
    display: flex;
    align-items: center;
}

.popup-content {
    background-color: #fefefe;
    margin: auto;
    padding: 20px;
    border: 1px solid #888;
    width: 70%;
}

make this responsive 
