# Android application security best practices
## Authorization
### AR-001 Check system permission
#### Overview
The **uses-permission** element requests a permission that the application must be granted in order for it to operate correctly. Permissions are granted by the user when the application is installed, not while it's running.
#### Code review
Check the **uses-permission** elements in AndroidManifest.xml.
#### Manual assessment
Use tools to review the permissions used in AndroidManifest.xml. For example, ManifestViewer in  Google Play Store. Or use Drozer command: `run app.package.info --package PACKAGE_NAME` to check the **Uses Permissions** section.
#### Excepted result
No excessive permissions are requested.

---
### AR-002 Check custom permission
#### Overview
Android allows to declare a custom permission that can be used to limit access to specific components or features of this or other applications. Android permissions are classified in four different categories based on the protection level it offers: Normal(by default), Dangerous, Signature and SystemOrSignature. Details can be referred to [https://developer.android.com/guide/topics/manifest/permission-element.html#plevel](https://developer.android.com/guide/topics/manifest/permission-element.html#plevel).
#### Code review
Check the **permission** elements in AndroidManifest.xml.
#### Manual assessment
Use tools to review the custom permission in AndroidManifest.xml. For example, use ManifestViewer in  Google Play Store to check the **permission** element. Or use Drozer command: `run app.package.info -a PACKAGE_NAME` to check the **Defines Permissions** section.
#### Excepted result
Custom permission should be used properly.

---
### AR-003 Check exported Activity
#### Overview
An activity represents a single screen with a user interface. Activity can be set with a permission attribute to require a certain permission when be started.

The android:exported element:
Whether or not the activity can be launched by components of other applications — "true" if it can be, and "false" if not. If "false", the activity can be launched only by components of the same application or applications with the same user ID.

The default value depends on whether the activity contains intent filters. The absence of any filters means that the activity can be invoked only by specifying its exact class name. So in this case, the default value is "false". On the other hand, the presence of at least one filter implies that the activity is intended for external use, so the default value is "true".

The permission element can also be used to limit the external entities that can send it messages.
#### Code review
Check the **activity** elements in AndroidManifest.xml.
#### Manual assessment
Use tools to check the activities. For example, use drozer command:
`run app.activity.info ––package PACKAGE_NAME`
#### Excepted result
The activity is not exported if it is not necessary. If it is exported, proper permission should be defined to restrict the access.

---
### AR-004 Check exported Service
#### Overview
Services are the background processing components in Android. It is possible to restrict who can interact with a service by applying permissions.

The android:exported element:
Whether or not components of other applications can invoke the service or interact with it — "true" if they can, and "false" if not. When the value is "false", only components of the same application or applications with the same user ID can start the service or bind to it.

The default value depends on whether the service contains intent filters. The absence of any filters means that it can be invoked only by specifying its exact class name. So in this case, the default value is "false". On the other hand, the presence of at least one filter implies that the service is intended for external use, so the default value is "true".

The permission element can also be used to limit the external entities that can send it messages.
#### Code review
Check the **service** elements in AndroidManifest.xml.
#### Manual assessment
Use tools to check the services. For example, use drozer command:
`run app.service.info ––package PACKAGE_NAME`
#### Excepted result
The service is not exported if it is not necessary. If it is exported, proper permission should be defined to restrict the access.

---
### AR-005 Check exported ContextProvider
#### Overview
Content Providers are the standard way for apps to make their data available to other apps. And Content Providers can specify two different required
permissions: one for reading and one for writing.
#### Code review
Check the **provider** elements in AndroidManifest.xml.
#### Manual assessment
Use tools to check the providers. For example, use drozer command:
`run app.provider.info –a PACKAGE_NAME`
and
`run scanner.provider.finduris -a PACKAGE_NAME`
#### Excepted result
The service is not exported if it is not necessary. If it is exported, proper permission should be defined to restrict the access.

---
### AR-006 Check exported BroadcastReceiver
#### Overview
Broadcast receivers are registered for specific events to occur.
The android:exported element:
Whether or not the broadcast receiver can receive messages from sources outside its application — "true" if it can, and "false" if not. If "false", the only messages the broadcast receiver can receive are those sent by components of the same application or applications with the same user ID.
The default value depends on whether the broadcast receiver contains intent filters. The absence of any filters means that it can be invoked only by Intent objects that specify its exact class name. So in this case, the default value is "false". On the other hand, the presence of at least one filter implies that the broadcast receiver is intended to receive intents broadcast by the system or other applications, so the default value is "true".

The permission element can also be used to limit the external entities that can send it messages.
#### Code review
Check the **receiver** elements in AndroidManifest.xml.
#### Manual assessment
Use tools to check the providers. For example, use drozer command:
`run app.broadcast.info -a PACKAGE_NAME -i`
#### Excepted result
The recevier is not exported if it is not necessary. If it is exported, proper permission should be defined to restrict the access.

---
## Configuration
### CF-001 Check Debugging mode
#### Overview
Android allows the attribute `android:debuggable` to be set to true so that the app can be debugged.  By default this attribute is disabled, i.e., it is set to false, but it may be set to true to help with debugging during development of the app.  
#### Code review
Check the value of `android:debuggable` in AndroidManifest.xml.
#### Manual assessment
Use tools to check whether the debug mode is enabled. For example, use Drozer command: `run app.package.debuggable -f PACKAGE_NAME` or run `app.package.attacksurface PACKAGE_NAME`.
#### Excepted result
Debug mode is disabled.

---
### CF-002 Check Backup flag
#### Overview
`android:allowBackup` allows the application to participate in the backup and restore infrastructure. The default value of this attribute is true.
#### Code review
Check the value of `android:allowBackup` in AndroidManifest.xml.
#### Manual assessment
Use tools to check whether backup is enabled or not. For example, use Drozer command: `run app.package.backup -f com.luxoft.androidctf`.
#### Excepted result
Backup is disabled. If it is enabled, make sure no sensitive data was included.

---
## Data Storage
### DS-001 Check data stored in SharedPreferences
#### Overview
The SharedPreferences provides a general framework to save and retrieve persistent key-value pairs in plain text within the app's private data directory.
#### Code review
Check the source code to verify the usage of SharedPreferences.
#### Manual assessment
Check the SharedPreferences files under `/data/data/PACKAGE_NAME /shared_prefs/`.
#### Excepted result
No sensitive data or user private data is stored in SharedPreferences files without protection.

---
### DS-002 Check data stored in SQLite databases
#### Overview
SQLite database is full supported by Android, but the data stored in it is not encrypted.
#### Code review
Check the usage of SQLiteDatabase. Such as: `execSQL()`.
#### Manual assessment
Check the SQLite DB files. For example, DB files under `/data/data/PACKAGE_NAME/` with the extension of `.db` or `.sqlite`.
#### Excepted result
No sensitive data or user private data is stored in SQLite databases without protection.

---
### DS-003 Check cache files
#### Overview
Temporary cache files will be cached and will be deleted when the device is low on internal storage space.
#### Code review
Check the usage of `getExternalCacheDir()` and `getCacheDir()`. **Note**: Manual accessment is necessary because code review is not sufficient.
#### Manual assessment
Check files under: `/data/data/<package name>/cache/` and `/sdcard/Android/data/PACKAGE_NAME/cache/`.
#### Excepted result
No sensitive data or user private data is cached.

---
### DS-004 Check other internal storage
#### Overview
Internal storage are private to your application and other applications cannot access them (nor can the user).
#### Code review
Check the usage of internal storage. For example, methods of `getFilesDir(), openFileOutput(), createTempFile()` and so on.
#### Manual assessment
Check files under `/data/data/PACKAGE_NAME/`.
#### Excepted result
No sensitive data or user private data is saved in internal storage without protection. And MODE_PRIVATE(by default) is used.

---
### DS-005 Check external storage
#### Overview
External storage is the best place for files that don't require access restrictions and for files that you want to share with other apps or allow the user to access with a computer.
#### Code review
Check all the IO operation to investigate the data saved to external storage. Such as `FileOutputStream, getExternalStorageDirectory(), getExternalStoragePublicDirectory()` and so on.
#### Manual assessment
Check the files saved in the SDCARD(under /sdcard) by the target app. Since there are too many files in the SDCARD, this can be done by monitor the file changes by tools like Inspeckage or check the file update time.
#### Excepted result
No sensitive data or user private data is saved in external storage

---
## Data Transmission
### DT-001 Check sensitive data is transmitted via secure channel
#### Overview
It is imperative that the communications between android applications and servers are performed in a proper encrypted channel.
#### Code review
Check the source code to investigate the data sent to the server. For example, the usage of `http://, HttpURLConnection,URLConnection, URL, TrustAllSSLSocketFactory, AllTrustSSLSocketFactory, NonValidatingSSLSocketFactory` in source code and so on. **Note**: Manual assessment is necessary since code review is not sufficient.
#### Manual assessment
Check the network communication by using any proxy tools. For example, Burpsuite or Fiddler.
#### Excepted result
Secure channel is used to transmit sensitive data.

---
### DT-002 Check the implementation of HTTPS
#### Overview
If custom `TrustManager` is used, make sure it is implemented properly.
#### Code review
Check the implementation of  `HostnameVerifier` and `X509TrustManager`.
#### Manual assessment
N/A
#### Excepted result
`ALLOW_ALL_HOSTNAME_VERIFIER` should not be set to `HostnameVerifier`. The `checkClientTrusted()` and `checkServerTrusted()` are implemented correctly

---
## Data Validation
### DV-001 Check JavaScript Execution in WebViews
#### Overview
JavaScript is disabled in a WebView by default, but can be enabled through the WebSettings.
#### Code review
Check the `setJavaScriptEnabled()` method in source code.
#### Manual assessment
N/A
#### Excepted result
JavaScript is disabled. If enabled, make sure no malicious code is executed.

---
### DV-002 Check resources included in WebViews
#### Overview
WebView supports  a number of  different file formats,  including:
-HTML
-PDF
-RTF
-Audio
-Image
-Video
Resource can be loaded by loaded through the method `loadURL()`. File access should be disabled by explicit if not need:
```Java
webView.getSettings().setAllowFileAccess(false);
webView.getSettings().setAllowFileAccessFromFileURLs(false);
webView.getSettings().setAllowUniversalAccessFromFileURLs(false);
webView.getSettings().setAllowContentAccess(false);
```
#### Code review
Check the `loadURL()` method in source code to verify what resource is loaded in Webview. If any dynamic parameters are used, make sure the parameters are validated.
#### Manual assessment
N/A
#### Excepted result
File access is disabled. If enabled, make sure proper validation is implemented.

---
### DV-003 Check Java Object exposed to WebViews
#### Overview
Android allows to export Java objects into a page's JavaScript context by using the `addJavascriptInterface()` method, so that they can be accessed by JavaScript in the page.
#### Code review
Check the `addJavascriptInterface()` method in source code.
#### Manual assessment
N/A
#### Excepted result
`JavascriptInterface` is not enabled. If enabled, make sure it will not be called by malicious code, such as XSS.
**Note**: JavaScript `alert()` function does not work on the raw WebView.

---
### DV-004 Check injection for SQLite databases
#### Overview
SQL injection is a code injection technique, used to attack data-driven applications, in which malicious SQL statements are inserted into an entry field for execution.
#### Code review
Check SQL queries to verify whether dynamic queries exist.
#### Manual assessment
Check every input to verify SQL injection attack.
**Note**: It will be more effective to verify this by code review.
#### Excepted result
Parameterized queries are used.

---
### DV-005 Check injection for Content Providers
#### Overview
As per Google’s inbuilt security model, application data is private to an application, hence it is not possible for an application to access another application’s data by default. When applications want to share their data with other applications, Content Provider is a way which acts as an interface for sharing data between applications.

Content providers use standard `insert()`, `query()`, `update()`, and `delete()` methods to access application data. A special form of URI which starts with `content://` is assigned to each content provider. Any app which knows this URI can insert, update, delete, and query data from the database of the provider app.
#### Code review
Check the `insert()`, `query()`, `update()`, and `delete()` methods of Content Provider in source code.
#### Manual assessment
Use tools to check injection attack for Content Provider. Fox example, use Drozer command:
`run scanner.provider.injection -a PACKAGE_NAME`.
#### Excepted result
Content Provider injection is protected.

---
### DV-006 Check code injection
#### Overview
`DexClassLoader` loads classes from .jar and .apk files containing a classes.dex entry. This can be used to execute code not installed as part of an application.

In the Android security model, the only security boundary is at the process level (not at the JVM level). So, nothing in Android prevents code injection into a process that you already control. As the Android docs state, untrusted code cannot be safely isolated within the Dalvik VM. That's just a fundamental consequence of Android's security architecture.
#### Code review
Check the usage of `DexClassLoader`(Or other ClassLoader used to load code dynamically).
#### Manual assessment
N/A
#### Excepted result
Dynamic code load should not be used. If used, make sure not code injection attacks exist.

---
## Logging
### LG-001 Check Android system log
#### Overview
Android logging system provides a mechanism for collecting and viewing system debug output. Log class is used to print out messages to the LogCat.
`Android.util.Log` offers five export functions: `Log.e()`, `Log.w()`, `Log.i()`, `Log.d()`, `Log.v()` for ERROR, WARN, INFO, DEBUG, VERBOSE debug types.

App logs can be accessed by requesting READ_LOGS permission in Android prior to 4.1.
In Android 4.1 and later version, App logs can not be accessed by other applications.
#### Code review
Check the usage of `Android.util.Log` in source code.
#### Manual assessment
Use tools to review the Android system log. For example, use adb command: `adb logcat -d -v long > /filePath/output.txt` or `adb logcat -s TAG_NAME:V > c:/ctf.log` to filter the log by tag name.
#### Excepted result
No sensitive data or user personal data is logged.

---
### LG-002 Check custom log
#### Overview
3rd party library for logging may be also been used(for statistics or other purposes).
#### Code review
Check the usage of custom or 3rd party logging.
#### Manual assessment
Use tools to review the Android system log console and local storage.
#### Excepted result
No sensitive data or user personal data is logged.

---
## Sensitive Data
### SD-001 Check sensitive data is masked in user interface
#### Overview
If sensitive data(password, credit card information and so on) is not masked properly, it could be disclosed in User Interfaces if displayed in plain text.
#### Code review
Check the `android:inputType="textPassword"` attribute is used for the `EditText` which includes sensitive data.
#### Manual assessment
Check the sensitve data displayed in user interfaces.
#### Excepted result
Sensitive data displayed in User Interfaces is protected.

---
### SD-002 Check sensitive data in the Clipboard
#### Overview
On Android, writing data to or reading data from the clipboard does not require any permission. Every app currently running on an Android device can read the items stored in the clipboard at any time.

So any text field including sensitive data should forbid copy/paste function.
#### Code review
Check the source code to verify that copy/paste is disabled for the inputs whith including sensitive data:
```Java
textField.setCustomSelectionActionModeCallback(new ActionMode.Callback() {
    public boolean onCreateActionMode(ActionMode actionMode, Menu menu) {
        return false;
    }
    public boolean onPrepareActionMode(ActionMode actionMode, Menu menu) {
        return false;
    }
    public boolean onActionItemClicked(ActionMode actionMode, MenuItem item) {
        return false;
    }
    public void onDestroyActionMode(ActionMode actionMode) {
    }
});
textField.setLongClickable(false);
textField.setTextIsSelectable(false);
```
#### Manual assessment
Perform long press on the inputs which includes sensitive data.
#### Excepted result
Copy/paste should be disabled for sensitive data.

---
### SD-003 Check sensitive data sent to third party
#### Overview
3rd party may collect data from the mobile application.
#### Code review
N/A
#### Manual assessment
Use proxy tools to verify the data collected by 3rd paties.
#### Excepted result
No sensitive data is collected by 3rd parties.

---
### SD-004 Check sensitive data in recent application list screenshots
#### Overview
Consider, whether the application needs to avoid having the contents of application’s activities from appearing in the recent application list.
#### Code review
Check the source code to verity:
```Java
getWindow().addFlags(WindowManager.LayoutParams.FLAG_SECURE);
```
#### Manual assessment
Open screens including sensitive data and then press recent key to verify whether it is captured in recent application list.
#### Excepted result
Sensitive data should not be captured in recent application list screenshots.

---
### SD-005 Check GeoLocation usage
#### Overview
If the application wants to receive location updates from **NETWORK_PROVIDER** or **GPS_PROVIDER**, the **ACCESS_COARSE_LOCATION** or **ACCESS_FINE_LOCATION** permission will be requested on AndroidManifest.xml.
#### Code review
Check the usage of GeoLocation, such as `getLastKnownLocation()`, `requestLocationUpdates()`, `getLatitude()`, `getLongitude()` in Source code.
#### Manual assessment
N/A
#### Excepted result
Application does not misuse and leak user's GeoLocation

---
### SD-006 Check sensitive message in Toast
#### Overview
A toast is a view containing a quick little message for the user.  So sensitive information disclosure will occur if sensitive data is displayed by Toast.
#### Code review
Check for presence of sensitive information in `Toast.makeText`.
#### Manual assessment
N/A
#### Excepted result
No sensitive message in Toast.

---
### SD-006 Check source code protection
#### Overview
As said, you will never get 100% security of implementation details while its implementation is being distributed to clients. So protection of source code can make it more difficult to reverse the source code of client side.
#### Code review
Check the source code to verify the source code protection. For example, ProGuard can be enabled in Gradle by using:
```Gradle
android {
    buildTypes {
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android.txt'),
                    'proguard-rules.pro'
        }
    }
    ...
}
```
#### Manual assessment
Use tools(such as **dex2jar** and **JD-GUI**) to reverse the APK file to review the source code.
#### Excepted result
Code protection should be implemented to protect sensitive data in the source code of client side.

---
