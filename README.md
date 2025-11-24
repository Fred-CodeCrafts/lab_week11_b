LAB WEEK 11 – CameraX & MediaStore (Part 3 Answers)
1. Code That Handles Where the Picture Is Stored

When the user takes a picture, the image is written into the MediaStore using the URI provided in the OutputFileOptions.
The responsible part of the code is:

val outputOptions = ImageCapture.OutputFileOptions.Builder(
    activity.contentResolver,
    MediaStore.Images.Media.EXTERNAL_CONTENT_URI,
    contentValues
).build()

imageCapture.takePicture(
    outputOptions,
    ContextCompat.getMainExecutor(activity),
    object : ImageCapture.OnImageSavedCallback {
        override fun onImageSaved(outputFileResults: ImageCapture.OutputFileResults) {
            val savedUri = outputFileResults.savedUri
            Log.d("Camera", "Image saved to: $savedUri")
        }

        override fun onError(exception: ImageCaptureException) {
            Log.e("Camera", "Image capture failed: ${exception.message}", exception)
        }
    }
)


This code:

Uses the generated URI

Passes it into OutputFileOptions

Allows CameraX to write the captured image directly into MediaStore

Returns the final saved URI through onImageSaved()

2. FileInfo.kt Attribute Explanations
• 1st Attribute — uri

The uri refers to the content URI pointing to the image stored inside the MediaStore.

Example:

content://media/external/images/media/1523


This URI uniquely identifies the image inside the system’s media database and is used to retrieve, display, or delete the image.

• 4th Attribute — relativePath

relativePath refers to the public folder location where the image will be stored.

Example:

Pictures/MyApp


This means the image will be placed under:

/storage/emulated/0/Pictures/MyApp/


Android uses this to organize media without requiring direct file-path access.

3. Bonus: Chronological Flow From Capture to Storage
Step-by-step timeline:

User taps the capture button
→ App calls imageCapture.takePicture().

Metadata is prepared
→ ContentValues is created (file name, MIME type, relativePath).

A pending MediaStore entry is created
→ MediaStore returns a writable URI.

CameraX writes image data
→ The image is written to the URI defined in OutputFileOptions.

Capture completes
→ onImageSaved() is called.
→ MediaStore finalizes the entry and makes the image visible in the Gallery.
