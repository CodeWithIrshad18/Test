<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.companyname.myapp">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.CAMERA" />

    <application android:allowBackup="true"
                 android:label="MyApp"
                 android:theme="@style/Maui.SplashTheme">
        <activity android:name="crc643f46942d9dd1fff9.MainActivity"
                  android:exported="true"
                  android:configChanges="keyboard|keyboardHidden|orientation|screenSize"
                  android:label="MyApp">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>

using Microsoft.Maui.ApplicationModel;

public async Task RequestPermissionsAsync()
{
    var statusCamera = await Permissions.RequestAsync<Permissions.Camera>();
    var statusLocation = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();

    if (statusCamera != PermissionStatus.Granted || statusLocation != PermissionStatus.Granted)
    {
        await App.Current.MainPage.DisplayAlert("Permissions", "Permissions not granted", "OK");
    }
}
protected override async void OnAppearing()
{
    base.OnAppearing();
    await RequestPermissionsAsync();
}
var location = await Geolocation.GetLastKnownLocationAsync();
if (location != null)
{
    Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}");
}




when i write yes getting this
Certificate was added to keystore
keytool error: java.io.FileNotFoundException: C:\Program Files\Android\Android Studio\jbr\lib\security\cacerts (Access is denied)
