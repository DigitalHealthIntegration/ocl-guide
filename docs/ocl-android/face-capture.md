# Biometric Library

This page describes how to use Biometric Library for capturing face images.

### Dependencies

The latest Biometric Library is available through [Maven Central Repository](https://central.sonatype.com/artifact/org.opencampaignlink/biometric-capture).

#### Gradle setup

Add the following dependency to your module's app-level build.gradle file, typically app/build.gradle:

```gradle
    dependencies {
        implementation("org.opencampaignlink:biometric-capture:0.0.1")
    }
```

#### Define an intent and launch FaceCapture Activity

```kotlin
    private val intentLauncher =
        registerForActivityResult(ActivityResultContracts.StartActivityForResult()) {
            val imagePath = it.data?.extras?.getString("path")
            // on successfull face capture, imagePath will have path to the file.
        }

    btnFaceCapture.setOnClickListener {
        val intent = Intent(this, FacePreCaptureActivity::class.java)
        intentLauncher.launch(intent)
    }
```


