package org.tsuisl.tsuislars

import android.Manifest
import android.annotation.SuppressLint
import android.os.Bundle
import android.webkit.*
import android.widget.Toast
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.activity.result.contract.ActivityResultContracts
import androidx.compose.foundation.Image
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.res.painterResource
import androidx.compose.ui.unit.dp
import androidx.compose.ui.viewinterop.AndroidView
import com.google.android.gms.location.*
import org.tsuisl.tsuislars.ui.theme.TSUISLARSTheme

class MainActivity : ComponentActivity() {

    private lateinit var fusedLocationClient: FusedLocationProviderClient

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)

        // ✅ Show UI immediately
        setContent {
            TSUISLARSTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    WebsiteScreen(
                        baseUrl = "https://services.tsuisl.co.in/TSUISLARS/",
                        fusedLocationClient = fusedLocationClient
                    )
                }
            }
        }
    }
}

@SuppressLint("SetJavaScriptEnabled")
@Composable
fun WebsiteScreen(
    baseUrl: String,
    fusedLocationClient: FusedLocationProviderClient
) {
    var isLoading by remember { mutableStateOf(true) }
    var webViewUrl by remember { mutableStateOf(baseUrl) }

    val permissionLauncher = rememberLauncherForActivityResult(
        contract = ActivityResultContracts.RequestMultiplePermissions(),
        onResult = { permissions ->
            val granted = permissions[Manifest.permission.ACCESS_FINE_LOCATION] == true ||
                    permissions[Manifest.permission.ACCESS_COARSE_LOCATION] == true
            if (granted) {
                fetchLocation(fusedLocationClient) { lat, lon ->
                    webViewUrl = "$baseUrl?lat=$lat&lon=$lon"
                }
            } else {
                Toast.makeText(
                    fusedLocationClient.applicationContext,
                    "Location permission denied — loading default view",
                    Toast.LENGTH_SHORT
                ).show()
            }
        }
    )

    // 🔹 Request permissions (non-blocking)
    LaunchedEffect(Unit) {
        permissionLauncher.launch(
            arrayOf(
                Manifest.permission.ACCESS_FINE_LOCATION,
                Manifest.permission.ACCESS_COARSE_LOCATION
            )
        )
    }

    Box(
        modifier = Modifier
            .fillMaxSize()
            .statusBarsPadding()
            .navigationBarsPadding()
    ) {
        // ✅ WebView is always visible
        AndroidView(
            factory = { context ->
                WebView(context).apply {
                    setBackgroundColor(android.graphics.Color.WHITE)
                    settings.apply {
                        javaScriptEnabled = true
                        domStorageEnabled = true
                        databaseEnabled = true
                        allowFileAccess = true
                        allowContentAccess = true
                        mixedContentMode = WebSettings.MIXED_CONTENT_ALWAYS_ALLOW
                        loadsImagesAutomatically = true
                        cacheMode = WebSettings.LOAD_NO_CACHE
                        useWideViewPort = true
                        loadWithOverviewMode = true
                    }

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
                            isLoading = false
                            Toast.makeText(context, "Failed to load page", Toast.LENGTH_SHORT).show()
                        }

                        override fun onReceivedSslError(
                            view: WebView?,
                            handler: SslErrorHandler?,
                            error: SslError?
                        ) {
                            handler?.proceed() // ⚠️ Only if your server uses valid HTTPS. Remove this in production if untrusted certs.
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

                    loadUrl(webViewUrl)
                }
            },
            update = { webView ->
                webView.loadUrl(webViewUrl)
            },
            modifier = Modifier.fillMaxSize()
        )

        // ✅ Overlay loading indicator on top instead of hiding WebView
        if (isLoading) {
            Column(
                modifier = Modifier
                    .fillMaxSize()
                    .background(Color.White.copy(alpha = 0.9f)),
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

@SuppressLint("MissingPermission")
private fun fetchLocation(
    fusedLocationClient: FusedLocationProviderClient,
    onLocationReceived: (Double, Double) -> Unit
) {
    fusedLocationClient.lastLocation.addOnSuccessListener { location ->
        if (location != null) {
            onLocationReceived(location.latitude, location.longitude)
        } else {
            fusedLocationClient.getCurrentLocation(
                Priority.PRIORITY_HIGH_ACCURACY,
                null
            ).addOnSuccessListener { newLocation ->
                if (newLocation != null)
                    onLocationReceived(newLocation.latitude, newLocation.longitude)
            }
        }
    }
}





this is my full code 
package org.tsuisl.tsuislars

import android.Manifest
import android.provider.Settings
import android.os.Bundle
import android.webkit.GeolocationPermissions
import android.webkit.PermissionRequest
import android.webkit.WebChromeClient
import android.webkit.WebSettings
import android.webkit.WebView
import android.webkit.WebViewClient
import android.widget.Toast
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.activity.result.contract.ActivityResultContracts
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Surface
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.viewinterop.AndroidView
import androidx.compose.foundation.Image
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.material3.CircularProgressIndicator
import androidx.compose.ui.Alignment
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.res.painterResource
import androidx.compose.ui.unit.dp
import android.view.View
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import android.webkit.*
import android.view.ViewGroup
import com.google.android.gms.location.FusedLocationProviderClient
import android.annotation.SuppressLint
import androidx.compose.ui.unit.dp
import com.google.android.gms.location.*
import com.google.android.gms.location.LocationServices
import org.tsuisl.tsuislars.ui.theme.TSUISLARSTheme

class MainActivity : ComponentActivity() {

    private lateinit var fusedLocationClient: FusedLocationProviderClient

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)

        // 🔹 Set the UI immediately (no waiting)
        setContent {
            TSUISLARSTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    WebsiteScreen(
                        baseUrl = "https://services.tsuisl.co.in/TSUISLARS/",
                        fusedLocationClient = fusedLocationClient
                    )
                }
            }
        }
    }
}

@SuppressLint("SetJavaScriptEnabled")
@Composable
fun WebsiteScreen(
    baseUrl: String,
    fusedLocationClient: FusedLocationProviderClient
) {
    var isLoading by remember { mutableStateOf(true) }
    var webViewUrl by remember { mutableStateOf(baseUrl) }

    val permissionLauncher = rememberLauncherForActivityResult(
        contract = ActivityResultContracts.RequestMultiplePermissions(),
        onResult = { permissions ->
            val granted = permissions[Manifest.permission.ACCESS_FINE_LOCATION] == true ||
                    permissions[Manifest.permission.ACCESS_COARSE_LOCATION] == true
            if (granted) {
                fetchLocation(fusedLocationClient) { lat, lon ->
                    webViewUrl = "$baseUrl?lat=$lat&lon=$lon"
                }
            }
        }
    )

    // 🔹 Request permissions in background (non-blocking)
    LaunchedEffect(Unit) {
        permissionLauncher.launch(
            arrayOf(
                Manifest.permission.ACCESS_FINE_LOCATION,
                Manifest.permission.ACCESS_COARSE_LOCATION
            )
        )
    }

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
                    setBackgroundColor(android.graphics.Color.WHITE)
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
                        allowFileAccess = true
                        allowContentAccess = true
                        mediaPlaybackRequiresUserGesture = false
                        mixedContentMode = WebSettings.MIXED_CONTENT_ALWAYS_ALLOW
                        loadsImagesAutomatically = true
                        cacheMode = WebSettings.LOAD_NO_CACHE
                        useWideViewPort = true
                        loadWithOverviewMode = true
                    }

                    loadUrl(webViewUrl)
                }
            },
            update = { webView ->
                webView.loadUrl(webViewUrl)
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

@SuppressLint("MissingPermission")
private fun fetchLocation(
    fusedLocationClient: FusedLocationProviderClient,
    onLocationReceived: (Double, Double) -> Unit
) {
    fusedLocationClient.lastLocation.addOnSuccessListener { location ->
        if (location != null) {
            onLocationReceived(location.latitude, location.longitude)
        } else {
            fusedLocationClient.getCurrentLocation(
                Priority.PRIORITY_HIGH_ACCURACY,
                null
            ).addOnSuccessListener { newLocation ->
                if (newLocation != null)
                    onLocationReceived(newLocation.latitude, newLocation.longitude)
            }
        }
    }
}
