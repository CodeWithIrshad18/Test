webViewClient = object : WebViewClient() {
    override fun onPageCommitVisible(view: WebView?, url: String?) {
        super.onPageCommitVisible(view, url)
        isLoading = false
    }

    override fun onReceivedError(
        view: WebView?,
        request: WebResourceRequest?,
        error: WebResourceError?
    ) {
        super.onReceivedError(view, request, error)
        isLoading = false
        Toast.makeText(
            context,
            "WebView Error: ${error?.description}",
            Toast.LENGTH_LONG
        ).show()
    }

    override fun onReceivedHttpError(
        view: WebView?,
        request: WebResourceRequest?,
        errorResponse: WebResourceResponse?
    ) {
        super.onReceivedHttpError(view, request, errorResponse)
        isLoading = false
        Toast.makeText(
            context,
            "HTTP Error: ${errorResponse?.statusCode}",
            Toast.LENGTH_LONG
        ).show()
    }
}




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
            if (allPermissionsGranted) {
                getVerifiedLocation()
            } else {
                Toast.makeText(this, "Please grant all permissions", Toast.LENGTH_LONG).show()
            }
        }

        permissionLauncher.launch(
            arrayOf(
                Manifest.permission.CAMERA,
                Manifest.permission.ACCESS_FINE_LOCATION,
                Manifest.permission.ACCESS_COARSE_LOCATION
            )
        )
    }

    @SuppressLint("MissingPermission")
    private fun getVerifiedLocation() {
        // Try last known location first (fast)
        fusedLocationClient.lastLocation.addOnSuccessListener { location ->
            if (location != null) {
                handleLocation(location)
            }
//            else {
//                // Fallback to fresh location if cache is empty
//                fusedLocationClient.getCurrentLocation(
//                    Priority.PRIORITY_BALANCED_POWER_ACCURACY,
//                    null
//                ).addOnSuccessListener { currentLocation ->
//                    if (currentLocation != null) {
//                        handleLocation(currentLocation)
//                    } else {
//                        Toast.makeText(this, "Unable to get location", Toast.LENGTH_SHORT).show()
//                    }
//                }
//            }
        }
    }

    private fun handleLocation(location: Location) {
        if (isLocationMocked(location) || isDeveloperModeEnabled()) {
            Toast.makeText(
                this,
                "Developer mode is on! Please turn it off",
                Toast.LENGTH_LONG
            ).show()
        } else {
            setUI(location.latitude, location.longitude)
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

    @SuppressLint("SetJavaScriptEnabled")
    private fun setUI(lat: Double, lon: Double) {
        // Remove immersive flags
        window.decorView.systemUiVisibility = (
                View.SYSTEM_UI_FLAG_LAYOUT_STABLE
                        or View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN
                        or View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION
                )

        setContent {
            TSUISLARSTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    WebsiteScreen(url = "https://services.tsuisl.co.in/TSUISLARS/?lat=$lat&lon=$lon")
                }
            }
        }
    }

    @Composable
    fun WebsiteScreen(url: String) {
        var isLoading by remember { mutableStateOf(true) }

        Box(
            modifier = Modifier
                .fillMaxSize()
                .statusBarsPadding()
                .navigationBarsPadding()
        ) {
            AndroidView(
                factory = { context ->
                    WebView(context).apply {
                        layoutParams = ViewGroup.LayoutParams(
                            ViewGroup.LayoutParams.MATCH_PARENT,
                            ViewGroup.LayoutParams.MATCH_PARENT
                        )
                        setBackgroundColor(android.graphics.Color.TRANSPARENT)

                        webViewClient = object : WebViewClient() {
                            override fun onPageCommitVisible(view: WebView?, url: String?) {
                                super.onPageCommitVisible(view, url)
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
                            databaseEnabled = true
                            mediaPlaybackRequiresUserGesture = false
                            allowFileAccess = true
                            allowContentAccess = true
                            mixedContentMode = WebSettings.MIXED_CONTENT_ALWAYS_ALLOW
                            loadsImagesAutomatically = true

                            // Optimize performance
                            cacheMode = WebSettings.LOAD_CACHE_ELSE_NETWORK
                            setSupportZoom(false)
                            builtInZoomControls = false
                            displayZoomControls = false
                            useWideViewPort = true
                            loadWithOverviewMode = true
                            javaScriptCanOpenWindowsAutomatically = true
                        }

                        loadUrl(url)
                    }
                },
                update = { webView ->
                    webView.visibility = if (isLoading) View.INVISIBLE else View.VISIBLE
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
