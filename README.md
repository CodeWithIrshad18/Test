getting this error 
Missing permissions required by ConnectivityManager.getActiveNetwork: android.permission.ACCESS_NETWORK_STATE

Missing permissions required by ConnectivityManager.getNetworkCapabilities: android.permission.ACCESS_NETWORK_STATE

on these two lines 

 val network = connectivityManager.activeNetwork ?: return false
        val capabilities = connectivityManager.getNetworkCapabilities(network) ?: return false
