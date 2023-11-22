# OCL App

This page describes how to use OCL App for QR Generation.

#### Install the latest OCL App

Ensure that users have the latest version of the OCL App installed on their devices. They can download it from the [Releases](https://github.com/DigitalHealthIntegration/ocl/releases) section of this repository.


#### Add Package Visibity filtering for Android 10 and above.
To ensure compatibility with Android 10 and above, add the following code to your `AndroidManifest.xml` file. This allows your app to query the presence of the OCL App:

```xml
    <queries>
        <package android:name="com.iprd.ocl_app" />
    </queries>
```

#### Define Input JSON for intent extras.
Prepare the input JSON that your app will send to the OCL App. This JSON should adhere to the request body schema of the [generateOcl](https://demo.opencampaignlink.org/swagger-ui/index.html#/OCL%20Related/generate) API.

```kotlin
    val inputJson = """
      {
        "campaignGuid": "7481ea14-05be-4de1-91dd-227b7c6826ed",
        "location": "12.912253,77.644405",
        "time": 1688123848,
        "userDefinedData": [
          {
            "k": "Patient Name",
            "v": "Peter"
          }
        ]
      }
    """.trimIndent()
```

Customize the input JSON based on the specific data you want to include in the QR code.

#### Define an intent launcher and invoke OCL App

Implement an intent launcher in your code to initiate the OCL App and pass the input JSON for QR code generation. Use the following Kotlin code:

```kotlin

    private val intentLauncher =
        registerForActivityResult(ActivityResultContracts.StartActivityForResult()) {
        val outputJson = it.data?.extras?.getString("output_json")
        // on successfull qr generation, outputJson will contain oclId and oclUrl
    }

    fun invokeOCLApp() {
        val intent = packageManager.getLaunchIntentForPackage("com.iprd.ocl_app")
        if (intent == null) {
            // OCL APP is not installed
            return
        }
        intent.flags = Intent.FLAG_ACTIVITY_CLEAR_TOP
        intent.putExtra(
            "input_json",
            inputJson
        )
        intentLauncher.launch(intent)
    }
```

This code snippet provides a robust way to invoke the OCL App, handling scenarios where the app might not be installed. Upon successful QR generation, the outputJson will contain essential information such as oclId and oclUrl.

