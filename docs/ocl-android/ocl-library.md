# OCL Library

This comprehensive guide outlines the steps to integrate and utilize the OCL Library for QR code generation in your Android application.

### Dependencies

Ensure that you have the latest version of the OCL Library, which is available through the [Maven Central Repository](https://central.sonatype.com/artifact/org.opencampaignlink/ocl).

#### Gradle setup

Add the following dependency to your module's app-level build.gradle file, typically app/build.gradle:

```gradle
dependencies {
    implementation("org.opencampaignlink:ocl:0.0.2")
}
```

#### Initialize the Library

To use OCL Library instance, initailize it with OCLLib.init() with context and configuration of SyncGateway which is part of OCL server side. This must be done only once, and we recommend doing this in the onCreate() function of your Application class.

```kotlin
    OCLLib.init(
      context,
      SyncGatewayConfig(
        URI("ws://10.102.10.91:5984/test-ocl"), //Couchbase SyncGateway server’s URL (including the port number and the name of the remote database to sync with)
        "username",
        "password"
      )
    )
```

#### Display the OCL Fragment
Add a FragmentContainerView to your activity's layout.

```xml
     <androidx.fragment.app.FragmentContainerView
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@+id/fragmentContainerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
```

Define the input json

Prepare the input JSON that your app will send to the OCL Library. This JSON should adhere to the request body schema of the [generateOcl](https://demo.opencampaignlink.org/swagger-ui/index.html#/OCL%20Related/generate) API. Below is a Kotlin example:

```kotlin
    val inputJson = """
      {
        "campaignGuid": "7481ea14-05be-4de1-91dd-227b7c6826ed",
        "location": "12.912253,77.644405",
        "time": 1688123848,
        "userDefinedData": [
          {
            "k": "Name",
            "v": "Peter"
          }
        ]
      }
    """.trimIndent()
```
Set the OCLFragment to the FragmentContainerView using FragmentManager.

```kotlin
    supportFragmentManager.commit {
        setReorderingAllowed(true)
        add(
          R.id.fragmentContainerView,
          OCLFragment::class.java,
          bundleOf(
            OCLFragment.BUNDLE_INPUT_JSON to inputJson
          ),
          OCLFragment::class.simpleName
        )
    }
```

Listen for the Fragment Result

```kotlin
    supportFragmentManager.setFragmentResultListener(OCLFragment.SUBMIT_REQUEST_KEY, this) { _ , bundle ->
      val outputJson = bundle.getString(OUTPUT_JSON)
      // on successfull qr generation, outputJson will contain oclId and oclUrl
    }
```

#### 2.4 Cleanup the OCL Library
Remember to cleanup the OCL Library instance when not in use, typically in the onDestroy() method of your activity.

```kotlin
    override fun onDestroy() {
        super.onDestroy()
        OCLLib.destroy()
    }
```

