I have this shared _Layout file code 

<body style="font-family:Arial;">
    <div class='dashboard'>
        <div class="dashboard-nav">
            <header>
                <a href="#!" class="menu-toggle"><i class="fas fa-bars"></i></a><a href="#"
                                                                                   class="brand-logo">
                    <span style="color:#fff;">TPR</span>
                </a>
            </header>
            <nav class="dashboard-nav-list">
                <a href="~/Technical/Homepage" class="dashboard-nav-item">
                    <i class="fas fa-home"></i>
                    Homepage
                </a>

               
                    <div class='dashboard-nav-dropdown'>
                        <a href="#!" class="dashboard-nav-item dashboard-nav-dropdown-toggle" style="color:#fff;">
                            <i class="fa fa-user" aria-hidden="true"></i> Admin
                        </a>
                        <div class='dashboard-nav-dropdown-menu'>
                             <a href="~/User/UserPermission"
                        class="dashboard-nav-dropdown-item">User Authorization</a>

                            <a href="~/User/Signup"
                               class="dashboard-nav-dropdown-item">Add New Member</a>

                        </div>
                    </div>
                <div class='dashboard-nav-dropdown'>
                    <a href="#!" class="dashboard-nav-item dashboard-nav-dropdown-toggle" style="color:#fff;">
                        <i class="fa fa-cog" aria-hidden="true"></i> Master
                    </a>
                    <div class='dashboard-nav-dropdown-menu'>
                        <a href="~/Master/SubjectMaster"
                           class="dashboard-nav-dropdown-item">Subject Master</a>

                    </div>
                </div>

               
                    <a href="~/Technical/Dashboard" class="dashboard-nav-item">
                    <i class="fas fa-folder-open"></i>
                        Dashboard
                    </a>
               

                   
               
              
                    <div class='dashboard-nav-dropdown'>
                       
                        <a href="#!" class="dashboard-nav-item dashboard-nav-dropdown-toggle" style="color:#fff;">
                        <i class="fa fa-users" aria-hidden="true"></i>Technical Services
                        </a>
                        <div class='dashboard-nav-dropdown-menu'>
                           
                                <a href="~/Technical/TechnicalService"
                                   class="dashboard-nav-dropdown-item">Upload Document</a>
                           
                        
                                <a href="~/Technical/EditDocument"
                                   class="dashboard-nav-dropdown-item">Edit Document</a>

                            

                        </div>
                    </div>
                            
                
            </nav>
        </div>
        <div class='dashboard-app'>

            <header class='dashboard-toolbar'>
                <a href="#!" class="menu-toggle" style="color:black;"><i class="fas fa-bars"></i></a>
                <div class="row justify-content-end user col-md-11">
                    <div class="col-sm-7 new" style="font-family:Arial;">
                      @*   <span style="font-size:20px;text-decoration:none;color:black;font-family:Arial;"><i class="fas fa-user-circle pno" style="margin-top:;"><span class="userid" style="font-family:Arial;font-weight:500;font-size:14px;">&nbsp;&nbsp; @UserName (@userId) </span></i></span> *@
                    </div>
                    <div class="col-sm-3 change" style="font-family:Arial;text-align:end;">
                        <a href="~/User/ChangePassword" style="font-size:14px;text-decoration:none;font-family:Arial;color:black;">
                            [Change Password]
                        </a>
                    </div>
                    <div class="col-sm-2 log" style="text-align:end;">
                        <a href="~/Technical/Logout" style="font-size:14px;text-decoration:none;font-family:Arial;color:black;" class="">
                            [ Log Out ]
                        </a>
                    </div>
                </div>
            </header>

            <div class="wrap" style="margin-top:;">

                @RenderBody()

            </div>
        </div>
    </div> 

and this is my homepage 
<div id="content" role="main">

    <section id="home">
        <div class="hero-wrap">
            <div class="hero-mask opacity-8 bg-dark"></div>
            <div class="hero-bg parallax" style="background-image:url('@Url.Content("~/images/intro-bg.jpg")');"></div>
            <div class="hero-content d-flex fullscreen py-5">
                <div class="container my-auto py-4">
                    <div class="row">
                       <div class="col-lg-12 text-center order-1 order-lg-0">
                            <img src="~/Images/img1.gif" style="width:20%;">
                          
                            <div class="typed-strings text-white fw-600 mb-0 text-uppercase h1">
                                <p>TPR Achievement through KPI</p>
                            </div>
                            
                            <h2 class="text-23 text-white fw-600 text-uppercase mb-0 ms-n1"><span class="typed"></span></h2>
                        </div>

                    </div>

                </div>
               
            </div>
        </div>
    </section>
</div>


issue is that homepage is upper at the sidebar why? see the image for your reference
