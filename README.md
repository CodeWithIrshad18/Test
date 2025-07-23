<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />

private val PERMISSIONS_REQUEST_CODE = 101
private val REQUIRED_PERMISSIONS = arrayOf(
    android.Manifest.permission.CAMERA,
    android.Manifest.permission.ACCESS_FINE_LOCATION,
    android.Manifest.permission.ACCESS_COARSE_LOCATION
)

private fun checkAndRequestPermissions() {
    val missingPermissions = REQUIRED_PERMISSIONS.filter {
        ContextCompat.checkSelfPermission(this, it) != PackageManager.PERMISSION_GRANTED
    }

    if (missingPermissions.isNotEmpty()) {
        ActivityCompat.requestPermissions(this, missingPermissions.toTypedArray(), PERMISSIONS_REQUEST_CODE)
    }
}


override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContent {
        // Your Compose UI code here
    }

    checkAndRequestPermissions()
}



WITH AttendanceAgg AS (
    SELECT
        AD.VendorCode,
        AD.WorkOrderNo,
        (
            SELECT EM.Sex
            FROM App_EmployeeMaster EM
            WHERE EM.AadharCard = AD.AadharNo
              AND EM.VendorCode = AD.VendorCode
              AND EM.WorkManSlNo = AD.WorkManSl
        ) AS Sex,
        (
            SELECT EM.Social_Category
            FROM App_EmployeeMaster EM
            WHERE EM.AadharCard = AD.AadharNo
              AND EM.VendorCode = AD.VendorCode
              AND EM.WorkManSlNo = AD.WorkManSl
        ) AS Social_Category,
        AD.WorkManCategory,
        COUNT(DISTINCT AD.AadharNo) AS TotalWorkers,
        SUM(CAST(AD.Present AS INT)) AS TotalMandays
    FROM App_AttendanceDetails AD
    WHERE AD.dates >= '2025-01-01' AND AD.dates < '2025-02-01'
    GROUP BY AD.VendorCode, AD.WorkOrderNo, AD.WorkManCategory
)




WITH AttendanceAgg AS (
    SELECT
        AD.VendorCode,
        AD.WorkOrderNo,
        EM.Sex,
        EM.Social_Category,
        AD.WorkManCategory,
        COUNT(DISTINCT EM.AadharCard) AS TotalWorkers,
        SUM(CAST(AD.Present AS INT)) AS TotalMandays
    FROM App_AttendanceDetails AD
    INNER JOIN App_EmployeeMaster EM
        ON EM.AadharCard = AD.AadharNo
        AND EM.VendorCode = AD.VendorCode
        AND EM.WorkManSlNo = AD.WorkManSl
    WHERE AD.dates >= '2025-01-01' AND AD.dates < '2025-02-01'
    GROUP BY AD.VendorCode, AD.WorkOrderNo, EM.Sex, EM.Social_Category, AD.WorkManCategory
),
