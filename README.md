## MediaPipe AAR Build Guide (For Mac, maybe Linux too)

* [Install Bazel](https://docs.bazel.build/versions/master/install.html)
* Clone or download copy of this repo or https://github.com/google/mediapipe
* Run the following:
      mkdir ./mediapipe/examples/android/src/java/com/google/mediapipe/apps/aarhandtracking
      touch ./mediapipe/examples/android/src/java/com/google/mediapipe/apps/aarhandtracking/BUILD
* Add following to the new `BUILD` file

      load("//mediapipe/java/com/google/mediapipe:mediapipe_aar.bzl", "mediapipe_aar")

      mediapipe_aar(
          name = "mp_hand_tracking_aar",
          calculators = ["//mediapipe/graphs/hand_tracking:mobile_calculators"],
      )

* Add the following snippet to `./mediapipe/java/com/google/mediapipe/mediapipe_aar.bzl`

      // under this definition
      def mediapipe_aar(name, calculators = [], assets = [], assets_dir = ""):

            ...

            _proto_java_src_generator(
                  name = "rect_proto",
                  proto_src = "mediapipe/framework/rect.proto",
                  java_lite_out = "com/google/mediapipe/formats/proto/RectProto.java",
                  srcs = ["//mediapipe/framework/formats:protos_src"],
            )

            ...

* Run the following to prepare our directories:

      mkdir -p ~/android/sdk
      mkdir -p ~/android/ndk
      export SDK_PATH="~/android/sdk"
      export NDK_PATH="~/android/ndk"

* Install SDK and NDK:
    * Go [here](https://developer.android.com/studio/index.html#command-tools) to find the latest command line tools, and copy the link to the latest download, e.g. https://dl.google.com/android/repository/commandlinetools-mac-6858069_latest.zip
          
          export SDK_DOWNLOAD_URL="{put url here}"
    
    * Go [here](https://developer.android.com/ndk/downloads) to find the latest NDK download, and copy the link to the latest download, e.g. https://dl.google.com/android/repository/android-ndk-r21d-darwin-x86_64.zip

          export NDK_DOWNLOAD_URL="{put url here}"

    * Copy the Latest Stable Version id, e.g. r21d

          export NDK_VERSION="{put version number here}"

* Now install the NDK and SDK with the script:

      mv /path/to/script/setup_android_sdk_and_ndk_modified.sh <mediapipe root dir> # e.g. ~/Downloads/mediapipe-master/
      cd <mediapipe root dir>
      bash ./setup_android_sdk_and_ndk.sh "${SDK_PATH}" "${NDK_PATH}" "${NDK_VERSION}" "${SDK_DOWNLOAD}" "${NDK_VERSION}"

* Run the following:

      export ANDROID_HOME="${SDK_PATH}"
      export ANDROID_NDK_HOME="${NDK_PATH}/android-ndk-${NDK_VERSION}"
      brew install opencv3
      brew install opencv@3

* Run the Bazel build command as such:

      bazel build -c opt --host_crosstool_top=@bazel_tools//tools/cpp:toolchain --fat_apk_cpu=arm64-v8a,armeabi-v7a //mediapipe/examples/android/src/java/com/google/mediapipe/apps/aarhandtracking:mp_hand_tracking_aar

* AAR file should build to `./mediapipe-master/bazel-bin/mediapipe/examples/android/src/java/com/google/mediapipe/apps/aarhandtracking`