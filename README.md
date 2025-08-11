package com.example.yourapp

import android.Manifest
import android.content.Context
import android.content.pm.PackageManager
import android.location.Location
import android.os.Build
import android.os.Bundle
import android.provider.Settings
import android.view.ViewGroup
import android.webkit.*
import android.widget.Toast
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.activity.result.contract.ActivityResultContracts
import androidx.compose.foundation.Image
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.material3.CircularProgressIndicator
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Surface
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.res.painterResource
import androidx.compose.ui.unit.dp
import androidx.core.app.ActivityCompat
import com.example.yourapp.ui.theme.YourTheme
import com.google.android.gms.location.FusedLocationProviderClient
import com.google.android.gms.location.LocationServices
import com.google.android.gms.location.Priority
import okhttp3.*
import org.json.JSONObject
import java.io.IOException

class MainActivity : ComponentActivity() {

    private lateinit var fusedLocationClient: FusedLocationProviderClient
    private var allPermissionsGranted = false
    private var deviceId: String = ""
    private var deviceModel: String = ""

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)

        // Get device details
        deviceId = Settings.Secure.getString(contentResolver, Settings.Secure.ANDROID_ID)
        deviceModel = Build.MODEL ?: "Unknown"

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
                    Toast.makeText(
                        this,
                        "Developer mode is on! Please disable Developer options",
                        Toast.LENGTH_LONG
                    ).show()
                } else {
                    sendDeviceInfoToServer(deviceId, deviceModel)
                    setUI(location.latitude, location.longitude)
                }
            } else {
                Toast.makeText(this, "Unable to get location", Toast.LENGTH_SHORT).show()
            }
        }
    }

    private fun sendDeviceInfoToServer(deviceId: String, deviceModel: String) {
        val client = OkHttpClient()
        val json = JSONObject()
        json.put("userId", "") // You can fill this after login via WebView/JSInterface
        json.put("deviceId", deviceId)
        json.put("deviceModel", deviceModel)

        val requestBody = RequestBody.create(
            "application/json; charset=utf-8".toMediaTypeOrNull(),
            json.toString()
        )

        val request = Request.Builder()
            .url("https://yourserver.com/api/device/register") // Your ASP.NET Core API endpoint
            .post(requestBody)
            .build()

        client.newCall(request).enqueue(object : Callback {
            override fun onFailure(call: Call, e: IOException) {
                runOnUiThread {
                    Toast.makeText(applicationContext, "Device check failed", Toast.LENGTH_SHORT).show()
                }
            }

            override fun onResponse(call: Call, response: Response) {
                if (!response.isSuccessful) {
                    runOnUiThread {
                        Toast.makeText(applicationContext, "Device not allowed", Toast.LENGTH_SHORT).show()
                    }
                }
            }
        })
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
            YourTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    WebsiteScreen(
                        url = "https://servicesdev.tsuisl.co.in/TSUISLARS/?lat=$lat&lon=$lon&deviceId=$deviceId&deviceModel=$deviceModel"
                    )
                }
            }
        }
    }

    @Composable
    fun WebsiteScreen(url: String) {
        var isLoading by remember { mutableStateOf(true) }
        val context = LocalContext.current

        Box(modifier = Modifier.fillMaxSize()) {
            AndroidView(
                factory = { ctx ->
                    WebView(ctx).apply {
                        layoutParams = ViewGroup.LayoutParams(
                            ViewGroup.LayoutParams.MATCH_PARENT,
                            ViewGroup.LayoutParams.MATCH_PARENT
                        )
                        settings.javaScriptEnabled = true
                        settings.domStorageEnabled = true
                        settings.setGeolocationEnabled(true)
                        webViewClient = object : WebViewClient() {
                            override fun onPageFinished(view: WebView?, url: String?) {
                                isLoading = false
                            }
                        }
                        webChromeClient = object : WebChromeClient() {
                            override fun onPermissionRequest(request: PermissionRequest?) {
                                request?.grant(request.resources)
                            }
                        }
                        loadUrl(url)
                    }
                },
                update = {},
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




import android.Manifest


import android.content.pm.PackageManager
import android.location.Location
import android.os.Bundle
import android.provider.Settings
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
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.res.painterResource
import androidx.compose.ui.unit.dp
import androidx.core.app.ActivityCompat
import com.google.android.gms.location.FusedLocationProviderClient
import com.google.android.gms.location.LocationServices
import com.google.android.gms.location.Priority
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch
import org.json.JSONObject
import java.net.HttpURLConnection
import java.net.URL

class MainActivity : ComponentActivity() {

    private lateinit var fusedLocationClient: FusedLocationProviderClient
    private var allPermissionsGranted = false
    private lateinit var deviceId: String

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)

        // Get unique device ID
        deviceId = Settings.Secure.getString(contentResolver, Settings.Secure.ANDROID_ID)

        val permissionLauncher = registerForActivityResult(
            ActivityResultContracts.RequestMultiplePermissions()
        ) { permissions ->
            allPermissionsGranted = permissions.all { it.value }
            if (!allPermissionsGranted) {
                Toast.makeText(this, "Please grant all permissions", Toast.LENGTH_LONG).show()
            } else {
                // First login check before loading UI
                attemptLoginAndProceed()
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

    private fun attemptLoginAndProceed() {
        CoroutineScope(Dispatchers.IO).launch {
            try {
                val apiUrl = "https://yourserver.com/api/auth/device-login" // <-- Replace with your ASP.NET Core endpoint
                val postData = JSONObject()
                postData.put("UserId", "testUser") // Replace with actual user input
                postData.put("Password", "123456") // Replace with actual user input
                postData.put("DeviceID", deviceId)

                val url = URL(apiUrl)
                val connection = url.openConnection() as HttpURLConnection
                connection.requestMethod = "POST"
                connection.setRequestProperty("Content-Type", "application/json; charset=UTF-8")
                connection.doOutput = true
                connection.outputStream.write(postData.toString().toByteArray())

                val responseCode = connection.responseCode
                if (responseCode == 200) {
                    // Allowed → proceed with location check
                    runOnUiThread { getVerifiedLocation() }
                } else {
                    // Rejected → show message
                    val errorMsg = connection.errorStream.bufferedReader().readText()
                    runOnUiThread {
                        Toast.makeText(
                            this@MainActivity,
                            "Login failed: $errorMsg",
                            Toast.LENGTH_LONG
                        ).show()
                    }
                }
                connection.disconnect()
            } catch (ex: Exception) {
                runOnUiThread {
                    Toast.makeText(
                        this@MainActivity,
                        "Login error: ${ex.message}",
                        Toast.LENGTH_LONG
                    ).show()
                }
            }
        }
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
                    Toast.makeText(
                        this,
                        "Developer mode is on! Please turn it off",
                        Toast.LENGTH_LONG
                    ).show()
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
        return android.provider.Settings.Secure.getInt(
            contentResolver,
            android.provider.Settings.Global.DEVELOPMENT_SETTINGS_ENABLED, 0
        ) != 0
    }

    private fun setUI(lat: Double, lon: Double) {
        setContent {
            WebsiteScreen(url = "https://servicesdev.tsuisl.co.in/TSUISLARS/?lat=$lat&lon=$lon")
        }
    }

    @Composable
    fun WebsiteScreen(url: String) {
        var isLoading by remember { mutableStateOf(true) }

        Box(modifier = Modifier.fillMaxSize()) {
            AndroidView(
                factory = { context ->
                    WebView(context).apply {
                        layoutParams = android.view.ViewGroup.LayoutParams(
                            android.view.ViewGroup.LayoutParams.MATCH_PARENT,
                            android.view.ViewGroup.LayoutParams.MATCH_PARENT
                        )
                        settings.javaScriptEnabled = true
                        settings.domStorageEnabled = true
                        settings.mediaPlaybackRequiresUserGesture = false
                        settings.allowFileAccess = true
                        settings.allowContentAccess = true
                        settings.setGeolocationEnabled(true)
                        settings.mixedContentMode = WebSettings.MIXED_CONTENT_ALWAYS_ALLOW
                        webViewClient = object : WebViewClient() {
                            override fun onPageFinished(view: WebView?, url: String?) {
                                isLoading = false
                            }
                        }
                        loadUrl(url)
                    }
                },
                update = { },
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




this is my current MainActivity.kt , please make changes to this 

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
