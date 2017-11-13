# Gstreamer_Android_Tutorials
This is a modified version of android tutorials from gst_docs that was tested to work with Android Studio 2.1 and Gstreamer Binaries version 1.4.4 in Ubuntu 16.04 LTS

**Pre-requisites**

1. Willingness to experiment.
2. Java JRE or JDK.

**OS**

Tested On Ubuntu 16.04 LTS (but should be OS independent)

Compiled with Android Studio 2.1

http://tools.android.com/download/studio/builds/android-studio-2-1-2

**Download and install the Gstreamer binary 1.4.4**

    cd /opt
    mkdir GST_ANDROID_SDK_1_4_4
    cd GST_ANDROID_SDK_1_4_4
    wget https://gstreamer.freedesktop.org/data/pkg/android/1.4.4/gstreamer-1.0-android-arm-debug-1.4.4.tar.bz2
    sudo tar xvf gstreamer-1.0-android-arm-debug-1.4.4.tar.bz2 
    sudo ln -sf GST_ANDROID_SDK_1_4_4 GST_ANDROID_SDK

**If using Linux open and modify**

    vim /opt/GST_ANDROID_SDK/share/gst-android/ndk-build/gstreamer-1.0.mk

**Modify to the following lines (Make should be aware of sed command's presence)**

    ifeq ($(HOST_OS),windows)
        HOST_SED := $(GSTREAMER_NDK_BUILD_PATH)/tools/windows/sed
        GSTREAMER_LD :=
    else
        HOST_SED := sed
    endif

**Then modify to the following**

    vim /opt/GST_ANDROID_SDK/share/gst-android/ndk-build/gstreamer_android-1.0.c.in
    
 And

       __android_log_print (ANDROID_LOG_ERROR, "GStreamer","%s", message);

**Installation**
For Android Studio see.

https://developer.android.com/studio/install.html

**SDK, NDK and JRE paths**

**File > Project Structure > SDK Location**

**SDK path**

    /home/<username>/Android/Sdk

**JRE path** (Assuming jdk1.8.0_92)

    /usr/local/java/jdk1.8.0_92

**NDK path**

    /home/<username>/Android/Sdk/ndk-bundle

**Settings > Appearance and Behavior > Android SDK > SDK Platforms** 

    Check everything from Android 2.3 -> Android N and click Apply
        
**Settings > Appearance and Behavior > Android SDK > SDK Tools**

    Check CMake, Check NDK, Check LLDB and click Apply.
