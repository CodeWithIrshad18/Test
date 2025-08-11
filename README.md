I have this is in my dotnet core Application 
    [HttpPost]
    public async Task<IActionResult> Login(AppLogin login)
    {
        if (!string.IsNullOrEmpty(login.UserId) && string.IsNullOrEmpty(login.Password))
        {
            ViewBag.FailedMsg = "Login Failed: Password is required";
            return View(login);
        }

        var user = await context.AppLogins
            .Where(x => x.UserId == login.UserId)
            .FirstOrDefaultAsync();

        if (user != null)
        {
            bool isPasswordValid = hash_Password.VerifyPassword(login.Password, user.Password, user.PasswordSalt);

            if (isPasswordValid)
            {
                
                string deviceId = login.DeviceID;  
                
                if (string.IsNullOrEmpty(deviceId))
                {
                    ViewBag.FailedMsg = "Device ID is required for login.";
                    return View(login);
                }

               
                var existingDevice = await context.AppDeviceDetails
.FirstOrDefaultAsync(d => d.UserId == login.UserId);


                if (existingDevice == null)
                {
                  
                    var newDevice = new AppDeviceDetails
                    {
                        Id = Guid.NewGuid(),
                        UserId = login.UserId,
                        DeviceID = deviceId,
                        DeviceModel = login.DeviceModel,
                        DeviceIDCount = 1
                    };

                    await context.AppDeviceDetails.AddAsync(newDevice);
                    await context.SaveChangesAsync();
                }

                else if (existingDevice.DeviceID != deviceId)
                {
                    ViewBag.FailedMsg = "Your account is already linked to another device.";
                    return View(login);
                }


               
                string query = @"
            SELECT EMA_PERNO, EMA_ENAME 
            FROM SAPHRDB.dbo.T_Empl_All 
            WHERE EMA_PERNO = @Pno";

                var parameters = new { Pno = login.UserId };
                EmpDTO userLoginData;

                using (var connection = GetRFIDConnectionString())
                {
                    await connection.OpenAsync();
                    userLoginData = await connection.QueryFirstOrDefaultAsync<EmpDTO>(query, parameters);
                }

                string userName = userLoginData?.EMA_ENAME ?? "Guest";
                string userPno = userLoginData?.EMA_PERNO ?? "N/A";

                HttpContext.Session.SetString("Session", userPno);
                HttpContext.Session.SetString("UserName", userName);
                HttpContext.Session.SetString("UserSession", login.UserId);

                var cookieOptions = new CookieOptions
                {
                    Expires = DateTimeOffset.Now.AddYears(1),
                    HttpOnly = false,
                    Secure = true,
                    IsEssential = true
                };

                Response.Cookies.Append("UserSession", login.UserId, cookieOptions);
                Response.Cookies.Append("Session", userPno, cookieOptions);
                Response.Cookies.Append("UserName", userName, cookieOptions);

                return RedirectToAction("GeoFencing", "Geo");
            }
            else
            {
                ViewBag.FailedMsg = "Login Failed: Incorrect password";
            }
        }
        else
        {
            ViewBag.FailedMsg = "Login Failed: User not found";
        }

        return View(login);
    }

    <div class="wrapper login">
        <h4 class="text-center">TSUISLARS</h4>

        <div class="input-box">
            <span class="icon"><i class='fas fa-user'></i></span>
            <input asp-for="UserId" type="number" oninput="javascript: if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);" maxlength="6" autocomplete="off">
            <label>Personal No.</label>
        </div>
        <div class="input-box">
            <span class="icon"><i class='fas fa-lock'></i></span>
            <input asp-for="Password" type="password" type="password" autocomplete="off">
            <label>Password</label>
        </div>

        <!-- Hidden fields for device info -->
    <input type="hidden" id="DeviceID" name="DeviceID" />
    <input type="hidden" id="DeviceModel" name="DeviceModel" />


        <div class="remember-forgot">
          
            <a href="~/User/ForgetPassword">Forgot Password?</a>
        </div>
        <div class="text-center">
            <button type="submit" class="btn btn-dark col-sm-4">LOG IN</button>
        </div>
    </div>
</form>

form side of dotnet core Application

<script type="module" src="https://unpkg.com/ionicons@7.1.0/dist/ionicons/ionicons.esm.js"></script>
<script nomodule src="https://unpkg.com/ionicons@7.1.0/dist/ionicons/ionicons.js"></script>

    <script>
    
    function getOrCreateDeviceID() {
        let deviceId = localStorage.getItem("DeviceID");
        if (!deviceId) {
            deviceId = crypto.randomUUID(); 
            localStorage.setItem("DeviceID", deviceId);
        }
        return deviceId;
    }

   
    function getDeviceModel() {
        return navigator.userAgent; 
    }

    document.getElementById("DeviceID").value = getOrCreateDeviceID();
    document.getElementById("DeviceModel").value = getDeviceModel();
</script>


and this is my app side 
class MainActivity : ComponentActivity() {

    private lateinit var fusedLocationClient: FusedLocationProviderClient
    private var allPermissionsGranted = false

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)

        val permissionLauncher = registerForActivityResult(
            ActivityResultContracts.RequestMultiplePermissions()
        ) { permissions ->
            allPermissionsGranted = permissions.all { it.value }
            if (!allPermissionsGranted) {
                Toast.makeText(this, "Please grant all permissions", Toast.LENGTH_LONG).show()
            }
            getVerifiedLocation()
        }

        permissionLauncher.launch(
            arrayOf(
                Manifest.permission.CAMERA,
                Manifest.permission.ACCESS_FINE_LOCATION,
                Manifest.permission.ACCESS_COARSE_LOCATION
            )
        )
    }

    private fun getVerifiedLocation() {
        if (ActivityCompat.checkSelfPermission(
                this,
                Manifest.permission.ACCESS_FINE_LOCATION
            ) != PackageManager.PERMISSION_GRANTED
        ) {
            return
        }

        fusedLocationClient.getCurrentLocation(
            Priority.PRIORITY_HIGH_ACCURACY,
            null
        ).addOnSuccessListener { location: Location? ->
            if (location != null) {
                if (isLocationMocked(location) || isDeveloperModeEnabled()) {
                    Toast.makeText(this, "Developer mode is on!Please off the Developer option", Toast.LENGTH_LONG).show()
                } else {
                    setUI(location.latitude, location.longitude)
                }
            } else {
                Toast.makeText(this, "Unable to get location", Toast.LENGTH_SHORT).show()
            }
        }
    }

    private fun isLocationMocked(location: Location): Boolean {
        return location.isFromMockProvider
    }

    private fun isDeveloperModeEnabled(): Boolean {
        return Settings.Secure.getInt(
            contentResolver,
            Settings.Global.DEVELOPMENT_SETTINGS_ENABLED, 0
        ) != 0
    }

    private fun setUI(lat: Double, lon: Double) {
        setContent {
            TSUISLARSTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    WebsiteScreen(url = "https://servicesdev.tsuisl.co.in/TSUISLARS/?lat=$lat&lon=$lon")
                }
            }
        }
    }

    @Composable
    fun WebsiteScreen(url: String) {
        var isLoading by remember { mutableStateOf(true) }

        Box(modifier = Modifier.fillMaxSize()) {
            AndroidView(
                factory = { context ->
                    WebView(context).apply {
                        layoutParams = ViewGroup.LayoutParams(
                            ViewGroup.LayoutParams.MATCH_PARENT,
                            ViewGroup.LayoutParams.MATCH_PARENT
                        )
                        setPadding(0, 0, 0, 0)
                        setBackgroundColor(android.graphics.Color.TRANSPARENT)

                        webViewClient = object : WebViewClient() {
                            override fun onPageFinished(view: WebView?, url: String?) {
                                super.onPageFinished(view, url)
                                isLoading = false
                            }
                        }

                        webChromeClient = object : WebChromeClient() {
                            override fun onPermissionRequest(request: PermissionRequest?) {
                                request?.grant(request.resources)
                            }

                            override fun onGeolocationPermissionsShowPrompt(
                                origin: String?,
                                callback: GeolocationPermissions.Callback?
                            ) {
                                callback?.invoke(origin, true, false)
                            }
                        }

                        settings.apply {
                            javaScriptEnabled = true
                            domStorageEnabled = true
                            mediaPlaybackRequiresUserGesture = false
                            allowFileAccess = true
                            allowContentAccess = true
                            setGeolocationEnabled(true)
                            mixedContentMode = WebSettings.MIXED_CONTENT_ALWAYS_ALLOW
                            loadsImagesAutomatically = true
                        }

                        clearCache(true)
                        clearHistory()
                        loadUrl(url)
                    }
                },
                update = { webView ->
                    if (isLoading) {
                        webView.visibility = android.view.View.INVISIBLE
                    } else {
                        webView.visibility = android.view.View.VISIBLE
                    }
                },
                modifier = Modifier.fillMaxSize()
            )

            if (isLoading) {
                Column(
                    modifier = Modifier
                        .fillMaxSize()
                        .background(Color.White),
                    verticalArrangement = Arrangement.Center,
                    horizontalAlignment = Alignment.CenterHorizontally
                ) {
                    Image(
                        painter = painterResource(id = R.drawable.logo),
                        contentDescription = "App Logo",
                        modifier = Modifier.size(120.dp)
                    )
                    Spacer(modifier = Modifier.height(20.dp))
                    CircularProgressIndicator()
                }
            }
        }
    }
}

is this works for one user one device login? is there anything to make changes
