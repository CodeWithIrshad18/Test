class MainActivity : ComponentActivity() {

    private lateinit var fusedLocationClient: FusedLocationProviderClient
    private var allPermissionsGranted = false

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)

        // ✅ Load app immediately (with default coordinates)
        setUI(0.0, 0.0)

        // Ask for permissions (non-blocking)
        val permissionLauncher = registerForActivityResult(
            ActivityResultContracts.RequestMultiplePermissions()
        ) { permissions ->
            allPermissionsGranted = permissions.all { it.value }
            if (allPermissionsGranted) {
                getVerifiedLocation()
            } else {
                Toast.makeText(this, "Location permission denied", Toast.LENGTH_SHORT).show()
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

    // ✅ Get device location (if available)
    @SuppressLint("MissingPermission")
    private fun getVerifiedLocation() {
        fusedLocationClient.lastLocation.addOnSuccessListener { location ->
            if (location != null) {
                handleLocation(location)
            } else {
                fusedLocationClient.getCurrentLocation(
                    com.google.android.gms.location.Priority.PRIORITY_HIGH_ACCURACY,
                    null
                ).addOnSuccessListener { newLoc ->
                    if (newLoc != null) handleLocation(newLoc)
                }
            }
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
            // ✅ Just reload URL (no full UI reload)
            updateWebViewWithLocation(location.latitude, location.longitude)
        }
    }

    private fun isLocationMocked(location: Location): Boolean = location.isFromMockProvider

    private fun isDeveloperModeEnabled(): Boolean {
        return Settings.Secure.getInt(
            contentResolver,
            Settings.Global.DEVELOPMENT_SETTINGS_ENABLED, 0
        ) != 0
    }

    // WebView reference to reload URL dynamically
    private var webViewRef: WebView? = null

    @SuppressLint("SetJavaScriptEnabled")
    private fun setUI(lat: Double, lon: Double) {
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
                    WebsiteScreen(
                        url = "https://services.tsuisl.co.in/TSUISLARS/?lat=$lat&lon=$lon",
                        onWebViewReady = { webViewRef = it }
                    )
                }
            }
        }
    }

    // ✅ Dynamically reload URL with location (no flicker)
    private fun updateWebViewWithLocation(lat: Double, lon: Double) {
        webViewRef?.post {
            webViewRef?.loadUrl("https://services.tsuisl.co.in/TSUISLARS/?lat=$lat&lon=$lon")
        }
    }

    // --------------------------------------------------

    @Composable
    fun WebsiteScreen(
        url: String,
        onWebViewReady: (WebView) -> Unit
    ) {
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
                        onWebViewReady(this) // give ref to activity

                        layoutParams = ViewGroup.LayoutParams(
                            ViewGroup.LayoutParams.MATCH_PARENT,
                            ViewGroup.LayoutParams.MATCH_PARENT
                        )
                        setBackgroundColor(android.graphics.Color.WHITE)

                        webViewClient = object : WebViewClient() {
                            override fun shouldOverrideUrlLoading(
                                view: WebView?,
                                request: WebResourceRequest?
                            ): Boolean {
                                val newUrl = request?.url?.toString() ?: return false
                                val safeUrl = if (newUrl.startsWith("http://")) {
                                    newUrl.replaceFirst("http://", "https://")
                                } else newUrl
                                view?.loadUrl(safeUrl)
                                return true
                            }

                            override fun onPageCommitVisible(view: WebView?, url: String?) {
                                super.onPageCommitVisible(view, url)
                                isLoading = false
                            }

                            override fun onReceivedError(
                                view: WebView?,
                                request: WebResourceRequest?,
                                error: WebResourceError?
                            ) {
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
                            cacheMode = WebSettings.LOAD_NO_CACHE
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
