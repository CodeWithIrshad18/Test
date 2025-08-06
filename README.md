<script>
    const userId = '@ViewBag.UserId';
    const userName = '@ViewBag.UserName';
</script>

<script>
    window.addEventListener("DOMContentLoaded", async () => {
        const video = document.getElementById("video");
        const canvas = document.getElementById("canvas");
        const capturedImage = document.getElementById("capturedImage");
        const EntryTypeInput = document.getElementById("EntryType");
        const statusText = document.getElementById("statusText");
        const videoContainer = document.getElementById("videoContainer");
        const punchInButton = document.getElementById("PunchIn");
        const punchOutButton = document.getElementById("PunchOut");

        if (punchInButton) punchInButton.style.display = "none";
        if (punchOutButton) punchOutButton.style.display = "none";

        await Promise.all([
            faceapi.nets.tinyFaceDetector.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceLandmark68Net.loadFromUri('/TSUISLARS/faceApi'),
            faceapi.nets.faceRecognitionNet.loadFromUri('/TSUISLARS/faceApi')
        ]);

        const safeUserName = userName.replace(/\s+/g, "%20");
        const timestamp = Date.now();

        const baseImageUrl = `/TSUISLARS/Images/${userId}-${safeUserName}.jpg?t=${timestamp}`;
        const capturedImageUrl = `/TSUISLARS/Images/${userId}-Captured.jpg?t=${timestamp}`;

        let baseDescriptor = null;
        let capturedDescriptor = null;

        try {
            baseDescriptor = await loadDescriptor(baseImageUrl);
            capturedDescriptor = await loadDescriptor(capturedImageUrl);
        } catch (err) {
            console.warn("Error loading descriptors:", err);
        }

        if (!baseDescriptor && !capturedDescriptor) {
            statusText.textContent = "❌ No reference image(s) found. Please upload your image.";
            return;
        }

        let faceMatcher = null;
        let matchMode = "";

        if (baseDescriptor && capturedDescriptor) {
            faceMatcher = new faceapi.FaceMatcher(
                [new faceapi.LabeledFaceDescriptors(userId, [baseDescriptor, capturedDescriptor])],
                0.35
            );
            matchMode = "both";
        } else if (baseDescriptor) {
            faceMatcher = new faceapi.FaceMatcher(
                [new faceapi.LabeledFaceDescriptors(userId, [baseDescriptor])],
                0.35
            );
            matchMode = "baseOnly";
        } else {
            statusText.textContent = "⚠️ Only captured image found. Cannot proceed with matching.";
            return;
        }

        startVideo();

        function startVideo() {
            navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } })
                .then(stream => {
                    video.srcObject = stream;
                    video.onloadeddata = () => requestAnimationFrame(detectAndMatchFace);
                })
                .catch(console.error);
        }

        let lastMismatchLoggedTime = 0;
        let matchFound = false;

        async function detectAndMatchFace() {
            if (matchFound) return;

            const detection = await faceapi
                .detectSingleFace(video, new faceapi.TinyFaceDetectorOptions({ inputSize: 320 }))
                .withFaceLandmarks()
                .withFaceDescriptor();

            if (!detection) {
                statusText.textContent = "No face detected";
                videoContainer.style.borderColor = "gray";
                return requestAnimationFrame(detectAndMatchFace);
            }

            const match = faceMatcher.findBestMatch(detection.descriptor);

            if (match.label === userId && match.distance < 0.35) {
                if (matchMode === "both") {
                    const distToBase = faceapi.euclideanDistance(detection.descriptor, baseDescriptor);
                    const distToCaptured = faceapi.euclideanDistance(detection.descriptor, capturedDescriptor);
                    if (distToBase < 0.35 && distToCaptured < 0.35) {
                        onMatchSuccess();
                    } else {
                        onMatchFailure();
                    }
                } else {
                    onMatchSuccess();
                }
            } else {
                onMatchFailure();
            }

            requestAnimationFrame(detectAndMatchFace);
        }

        function onMatchSuccess() {
            statusText.textContent = `${userName}, Face matched ✅`;
            matchFound = true;
            videoContainer.style.borderColor = "green";
            setTimeout(() => {
                showSuccessAndCapture();
            }, 1000);
        }

        function onMatchFailure() {
            statusText.textContent = "Face not matched ❌";
            videoContainer.style.borderColor = "red";

            const now = Date.now();
            const cooldownMs = 5000;

            if (now - lastMismatchLoggedTime >= cooldownMs) {
                lastMismatchLoggedTime = now;

                const entryType = document.getElementById("Entry")?.value || "";
                fetch("/TSUISLARS/Geo/LogFaceMatchFailure", {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ Type: entryType })
                });
            }
        }

        function showSuccessAndCapture() {
            const captureCanvas = document.createElement("canvas");
            captureCanvas.width = video.videoWidth;
            captureCanvas.height = video.videoHeight;

            const ctx = captureCanvas.getContext("2d");
            ctx.translate(captureCanvas.width, 0);
            ctx.scale(-1, 1);
            ctx.drawImage(video, 0, 0, captureCanvas.width, captureCanvas.height);

            const imageCaptured = captureCanvas.toDataURL("image/jpeg");
            capturedImage.src = imageCaptured;
            capturedImage.style.display = "block";
            video.style.display = "none";

            if (punchInButton) punchInButton.style.display = "inline-block";
            if (punchOutButton) punchOutButton.style.display = "inline-block";

            window.capturedDataURL = imageCaptured;
        }

        async function loadDescriptor(imageUrl) {
            try {
                const img = await faceapi.fetchImage(imageUrl);
                const detection = await faceapi
                    .detectSingleFace(img, new faceapi.TinyFaceDetectorOptions())
                    .withFaceLandmarks()
                    .withFaceDescriptor();
                return detection?.descriptor || null;
            } catch (err) {
                console.warn(`Error loading descriptor from ${imageUrl}:`, err);
                return null;
            }
        }

        // ✅ Updated Function: verify face again from captured image before submission
        window.captureImageAndSubmit = async function (entryType) {
            if (!window.capturedDataURL) {
                alert("❌ No image captured.");
                statusText.textContent = "Please try again — no image captured.";
                return;
            }

            statusText.textContent = "🔍 Verifying captured image before submission...";

            try {
                const img = await faceapi.fetchImage(window.capturedDataURL);
                const detection = await faceapi
                    .detectSingleFace(img, new faceapi.TinyFaceDetectorOptions({ inputSize: 320 }))
                    .withFaceLandmarks()
                    .withFaceDescriptor();

                if (!detection) {
                    statusText.textContent = "❌ No face found in captured image.";
                    videoContainer.style.borderColor = "gray";
                    return;
                }

                const match = faceMatcher.findBestMatch(detection.descriptor);

                if (match.label === userId && match.distance < 0.35) {
                    if (matchMode === "both") {
                        const distToBase = faceapi.euclideanDistance(detection.descriptor, baseDescriptor);
                        const distToCaptured = faceapi.euclideanDistance(detection.descriptor, capturedDescriptor);
                        if (distToBase >= 0.35 || distToCaptured >= 0.35) {
                            statusText.textContent = "❌ Final match failed. Please retry.";
                            videoContainer.style.borderColor = "red";
                            return;
                        }
                    }

                    // ✅ Final match passed
                    statusText.textContent = "✅ Verified! Submitting...";
                    EntryTypeInput.value = entryType;

                    Swal.fire({
                        title: "Please wait...",
                        allowOutsideClick: false,
                        showConfirmButton: false,
                        didOpen: () => Swal.showLoading()
                    });

                    fetch("/TSUISLARS/Geo/AttendanceData", {
                        method: "POST",
                        headers: { "Content-Type": "application/json" },
                        body: JSON.stringify({ Type: entryType, ImageData: window.capturedDataURL })
                    })
                        .then(res => res.json())
                        .then(data => {
                            const now = new Date().toLocaleString();
                            if (data.success) {
                                statusText.textContent = "";
                                Swal.fire("Thank you!", `Attendance Recorded.\nDate & Time: ${now}`, "success")
                                    .then(() => location.reload());
                            } else {
                                Swal.fire("Face Verified, But Error!", "Server rejected attendance.", "error")
                                    .then(() => location.reload());
                            }
                        })
                        .catch(() => {
                            Swal.fire("Error!", "Submission failed.", "error");
                        });

                } else {
                    // ❌ Final check failed
                    statusText.textContent = "❌ Final face check failed. Please try again.";
                    videoContainer.style.borderColor = "red";

                    if (punchInButton) punchInButton.style.display = "none";
                    if (punchOutButton) punchOutButton.style.display = "none";
                    capturedImage.style.display = "none";
                    video.style.display = "block";
                    matchFound = false;
                    requestAnimationFrame(detectAndMatchFace);
                }

            } catch (err) {
                console.error("Error during final verification:", err);
                statusText.textContent = "❌ Error during final verification. Please try again.";
            }
        };
    });
</script>



package org.tsuisl.tsuislars

import android.Manifest
import android.content.Context
import android.location.Location
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
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.viewinterop.AndroidView
import com.google.android.gms.location.FusedLocationProviderClient
import com.google.android.gms.location.LocationServices
import org.tsuisl.tsuislars.ui.theme.TSUISLARSTheme

class MainActivity : ComponentActivity() {

    private var allPermissionsGranted = false
    private lateinit var fusedLocationClient: FusedLocationProviderClient

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)

        val permissionLauncher = registerForActivityResult(
            ActivityResultContracts.RequestMultiplePermissions()
        ) { permissions ->
            allPermissionsGranted = permissions.all { it.value }
            if (!allPermissionsGranted) {
                Toast.makeText(this, "Please grant all permissions", Toast.LENGTH_LONG).show()
            } else {
                checkMockLocation()
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

    private fun checkMockLocation() {
        fusedLocationClient.lastLocation.addOnSuccessListener { location: Location? ->
            if (location != null) {
                if (location.isFromMockProvider) {
                    Toast.makeText(this, "Fake location detected!", Toast.LENGTH_LONG).show()
                } else {
                    setUI()
                }
            } else {
                Toast.makeText(this, "Location not available", Toast.LENGTH_SHORT).show()
            }
        }.addOnFailureListener {
            Toast.makeText(this, "Failed to get location", Toast.LENGTH_SHORT).show()
        }
    }

    private fun setUI() {
        setContent {
            TSUISLARSTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    WebsiteScreen(url = "https://services.tsuisl.co.in/TSUISLARS/")
                }
            }
        }
    }

    private fun getNavigationBarHeight(context: Context): Int {
        val resources = context.resources
        val resourceId = resources.getIdentifier("navigation_bar_height", "dimen", "android")
        return if (resourceId > 0) resources.getDimensionPixelSize(resourceId) else 0
    }

    @Composable
    fun WebsiteScreen(url: String) {
        val context = LocalContext.current
        val navBarHeight = remember { getNavigationBarHeight(context) }

        AndroidView(factory = {
            WebView(it).apply {
                setPadding(0, 0, 0, navBarHeight)

                webViewClient = WebViewClient()
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
        })
    }
}




I have this main Activity 
package org.tsuisl.tsuislars

import android.Manifest
import android.content.Context
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
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.viewinterop.AndroidView
import org.tsuisl.tsuislars.ui.theme.TSUISLARSTheme

class MainActivity : ComponentActivity() {

    private var allPermissionsGranted = false

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val permissionLauncher = registerForActivityResult(
            ActivityResultContracts.RequestMultiplePermissions()
        ) { permissions ->
            allPermissionsGranted = permissions.all { it.value }
            if (!allPermissionsGranted) {
                Toast.makeText(this, "Please grant all permissions", Toast.LENGTH_LONG).show()
            }
            setUI()
        }

        permissionLauncher.launch(
            arrayOf(
                Manifest.permission.CAMERA,
                Manifest.permission.ACCESS_FINE_LOCATION,
                Manifest.permission.ACCESS_COARSE_LOCATION
            )
        )
    }

    private fun setUI() {
        setContent {
            TSUISLARSTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    WebsiteScreen(url = "https://services.tsuisl.co.in/TSUISLARS/")
                }
            }
        }
    }

    private fun getNavigationBarHeight(context: Context): Int {
        val resources = context.resources
        val resourceId = resources.getIdentifier("navigation_bar_height", "dimen", "android")
        return if (resourceId > 0) resources.getDimensionPixelSize(resourceId) else 0
    }

    @Composable
    fun WebsiteScreen(url: String) {
        val context = LocalContext.current
        val navBarHeight = remember { getNavigationBarHeight(context) }

        AndroidView(factory = {
            WebView(it).apply {
                setPadding(0, 0, 0, navBarHeight)

                webViewClient = WebViewClient()
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
        })
    }
}

and this is my android manifest 

<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="org.tsuisl.tsuislars">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />

    <uses-feature android:name="android.hardware.camera" android:required="false" />
    <uses-feature android:name="android.hardware.location.gps" android:required="false" />

    <application
        android:allowBackup="true"
        android:usesCleartextTraffic="true"
        android:hardwareAccelerated="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/Theme.TSUISLARS">

        <activity
            android:name=".MainActivity"
            android:exported="true"
            android:theme="@style/Theme.TSUISLARS">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

    </application>
</manifest>

please make changes to this code 
