## MediaPipe AAR Build Guide (For Mac/Linux)

This repo is meant to be a guide for building an MediaPipe plugin for Android, specifically to be used in Unity.
This example uses Hand Tracking, but other dependencies can probably also be subbed in easily.
Please reference [this official guide](https://google.github.io/mediapipe/getting_started/android_archive_library.html) for more info.

_Note: I haven't verified that this works with Linux._

1. [Install Bazel](https://docs.bazel.build/versions/master/install.html)
2. Clone or download copy of this repo or https://github.com/google/mediapipe
      * If you clone this repo, skip to Step 6
3. Run the following:

       mkdir ./mediapipe/examples/android/src/java/com/google/mediapipe/apps/aarhandtracking
       touch ./mediapipe/examples/android/src/java/com/google/mediapipe/apps/aarhandtracking/BUILD

4. Add following to the new `BUILD` file:

       load("//mediapipe/java/com/google/mediapipe:mediapipe_aar.bzl", "mediapipe_aar")

       mediapipe_aar(
           name = "mp_hand_tracking_aar",
           calculators = ["//mediapipe/graphs/hand_tracking:mobile_calculators"],
       )

5. Add the following snippet to `./mediapipe/java/com/google/mediapipe/mediapipe_aar.bzl`

       def mediapipe_aar(name, calculators = [], assets = [], assets_dir = ""):

             ...

             _proto_java_src_generator(
                   name = "rect_proto",
                   proto_src = "mediapipe/framework/rect.proto",
                   java_lite_out = "com/google/mediapipe/formats/proto/RectProto.java",
                   srcs = ["//mediapipe/framework/formats:protos_src"],
             )

             ...

6. Run the following to prepare our directories (you can delete these after you're done):

       export SDK_PATH="${HOME}/android/sdk"
       export NDK_PATH="${HOME}/android/ndk"
       mkdir -p "${SDK_PATH}"
       mkdir -p "${NDK_PATH}"

7. Install SDK and NDK:
      * Go [here](https://developer.android.com/studio/index.html#command-tools) to find the latest command line tools, and copy the link to the latest download, e.g. https://dl.google.com/android/repository/commandlinetools-mac-6858069_latest.zip
          
            export SDK_DOWNLOAD_URL="{put url here}"
      * Go [here](https://developer.android.com/ndk/downloads) to find the latest NDK download, and copy the link to the latest download, e.g. https://dl.google.com/android/repository/android-ndk-r21d-darwin-x86_64.zip

            export NDK_DOWNLOAD_URL="{put url here}"

      * Copy the Latest Stable Version id, e.g. `r21d`

            export NDK_VERSION="{put version number here}"

8. Now install the NDK and SDK with the script:

       bash ./setup_android_sdk_and_ndk_modified.sh "${SDK_PATH}" "${NDK_PATH}" "${NDK_VERSION}" "${SDK_DOWNLOAD_URL}" "${NDK_DOWNLOAD_URL}"

9. Run the following:

       export ANDROID_HOME="${SDK_PATH}"
       export ANDROID_NDK_HOME="${NDK_PATH}/android-ndk-${NDK_VERSION}"
       brew install opencv3
       brew install opencv@3

10. Run the Bazel build command as such:

        bazel build -c opt --host_crosstool_top=@bazel_tools//tools/cpp:toolchain --fat_apk_cpu=arm64-v8a,armeabi-v7a //mediapipe/examples/android/src/java/com/google/mediapipe/apps/aarhandtracking:mp_hand_tracking_aar

AAR file should build to `./bazel-bin/mediapipe/examples/android/src/java/com/google/mediapipe/apps/aarhandtracking`
