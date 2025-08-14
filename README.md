@SuppressLint("SetJavaScriptEnabled")
private fun setUI(lat: Double, lon: Double) {
    // Keep status and nav bars visible, allow layout behind them
    window.decorView.systemUiVisibility = (
        View.SYSTEM_UI_FLAG_LAYOUT_STABLE
        or View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION
        or View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN
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
            .statusBarsPadding()        // prevent top overlap
            .padding(bottom = 56.dp)    // space for your navbar above system nav
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
                webView.visibility = if (isLoading) View.INVISIBLE else View.VISIBLE
            },
            modifier = Modifier.fillMaxSize()
        )

        // Your custom navbar
        Box(
            modifier = Modifier
                .align(Alignment.BottomCenter)
                .fillMaxWidth()
                .height(56.dp)
                .background(Color(0xFF1976D2)),
            contentAlignment = Alignment.Center
        ) {
            Text(
                text = "My Navbar",
                color = Color.White,
                style = MaterialTheme.typography.bodyLarge
            )
        }

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

