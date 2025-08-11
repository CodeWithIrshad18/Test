getting this error when releasing apk

Lint found fatal errors while assembling a release target.

Fix the issues identified by lint, or create a baseline to see only new errors.
To create a baseline, run `gradlew updateLintBaseline` after adding the following to the module's build.gradle file:
```
android {
    lint {
        baseline = file("lint-baseline.xml")
    }
}
```
For more details, see https://developer.android.com/studio/write/lint#snapshot

C:\Users\EWEPA7818A\AndroidStudioProjects\TSUISLARS2\app\src\main\java\com\example\tsuislars\MainActivity.kt:53: Error: Upgrade Fragment version to at least 1.3.0. [InvalidFragmentVersionForActivityResult from androidx.activity]
        val permissionLauncher = registerForActivityResult(
                                 ^

   Explanation for issues of type "InvalidFragmentVersionForActivityResult":
   In order to use the ActivityResult APIs you must upgrade your              
     Fragment version to 1.3.0. Previous versions of FragmentActivity         
          failed to call super.onRequestPermissionsResult() and used invalid
   request codes

   https://developer.android.com/training/permissions/requesting#make-the-request

   Vendor: Android Open Source Project
   Identifier: androidx.activity
   Feedback: https://issuetracker.google.com/issues/new?component=527362

1 error
