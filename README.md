getting this error Gradle Project 

plugins {
    alias(libs.plugins.android.application) apply false
    alias(libs.plugins.kotlin.android) apply false
    alias(libs.plugins.kotlin.compose) apply false
}


Build file 'D:\Irshad(DotNet Core)\APK\build.gradle.kts' line: 2

Plugin [id: 'com.android.application', version: '8.11.1', apply: false] was not found in any of the following sources:

* Try:
> Run with --info or --debug option to get more log output.
> Run with --scan to get full insights.
> Get more help at https://help.gradle.org.

* Exception is:
org.gradle.api.plugins.UnknownPluginException: Plugin [id: 'com.android.application', version: '8.11.1', apply: false] was not found in any of the following sources:

- Gradle Core Plugins (plugin is not in 'org.gradle' namespace)
- Included Builds (No included builds contain this plugin)
- Plugin Repositories (could not resolve plugin artifact 'com.android.application:com.android.application.gradle.plugin:8.11.1')
  Searched in the following repositories:
