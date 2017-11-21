# Gstreamer_Android_Tutorials
This is a modified version of android tutorials from gst_docs (Jni and Java) that was tested to work with Android Studio 2.1 and Gstreamer Binaries version 1.4.4 in Ubuntu 16.04 LTS

**Pre-requisites**

1. Willingness to experiment.
2. Java JRE or JDK.

**OS**

Tested On Ubuntu 16.04 LTS (but should be OS independent)

Compiled with Android Studio 2.1 and Android Studio 3.0

Download the files from the following URL

**Hosted at:**

https://developer.android.com/studio/archive.html

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
    
**In the same file gstreamer-1.0.mk modify the following**

    #GSTREAMER_LD                  := -fuse-ld=gold  #<---------- comment out this line
    
    #Add the following line instead
    GSTREAMER_LD                  := -fuse-ld=gold -Wl,-soname,lib$(GSTREAMER_ANDROID_MODULE_NAME).so


**Then modify to the following**

    vim /opt/GST_ANDROID_SDK/share/gst-android/ndk-build/gstreamer_android-1.0.c.in
    
 and ensure the line is updated as follows

       __android_log_print (ANDROID_LOG_ERROR, "GStreamer","%s", message);

**Installation**
For Android Studio see.

https://developer.android.com/studio/install.html

**SDK, NDK and JRE paths**

**File > Project Structure > SDK Location**

**SDK path**

    /home/<username>/Android/Sdk

**JRE path** (Assuming jdk1.8.0_92) **Note: One may also leave this to default Android Studio's embedded JDK**

    /usr/local/java/jdk1.8.0_92

**NDK path**

    /home/<username>/Android/Sdk/ndk-bundle
  
**For every Project's local.properties file update the following information (or to appropriate installation directories if not default location)**
    
    ndk.dir=/home/<your home directory username>/Android/Sdk/ndk-bundle
    sdk.dir=/home/<your home directory username>/Android/Sdk

**Settings > Appearance and Behavior > System Settings > Android SDK > SDK Platforms** 

    Check everything from Android 2.3 -> Android N and click Apply
        
**Settings > Appearance and Behavior > System Settings > Android SDK > SDK Tools**

    Check CMake, Check NDK, Check LLDB (all versions for all 3 packages if allowed) and click Apply.
        
**Add PATH in /etc/bash.bashrc**

    export GSTREAMER_ROOT_ANDROID=/opt/GST_ANDROID_SDK


**Settings > Appearance and Behavior > System Settings > Path Variables**

    GSTREAMER_ROOT_ANDROID (In the Name Field)  /opt/GST_ANDROID_SDK (In the Value Field)
    
    
**Update:**    

**Changes to get the examples to work in Android Studio 3.0**

Update *build.gradle (app)* as follows

    //def ndkDir = project.plugins.findPlugin('com.android.application').getNdkFolder()
    //def ndkDir = plugins.getPlugin('com.android.application').sdkHandler.ndkFolder
    def ndkDir = project.android.ndkDirectory
    
as well as the following lines in *build.gradle (app)*
     
     //println(project.plugins.findPlugin('com.android.application').getNdkFolder())
     //println(plugins.getPlugin('com.android.application').sdkHandler.ndkFolder)
     println(project.android.ndkDirectory)

Ensure the following line is present in *gradle-wrapper.properties*

    distributionUrl=https\://services.gradle.org/distributions/gradle-4.1-all.zip
    
Ensure the following line is present in *build.gradle(Project: android-tutorial-<x>)* 
    
    jcenter()
    google() #<--------- Add this to the list of repositories

    dependencies {
        classpath 'com.android.tools.build:gradle:3.0.0'
    }
    
    
**Trouble-shooting**

One can check error from the project's parent directory using the tool *gradlew* especially for ndk build files.

    ./gradlew  tasks app:ndkLibsToJar app:ndkBuild app:assembleDebug --stacktrace

Android Studio caching mechanism is weird. I have tried to always perform "sync" wherever recommended after running the above command. I have also ended up "File > Synchronize" and "File > Invalidate Caches/ Restart" depending on the version of Android Studio.

**I have been having trouble with ndk 16 (r16) (jni.h not found) so I reverted to ndk 15 (r15c)** 

I read the announcements in r16 which I replicate below 


        The deprecated headers have been removed. Unified Headers are now simply "The Headers". For migration tips, see Unified Headers Migration Notes.
        
        GCC is no longer supported. It will not be removed from the NDK just yet, but is no longer receiving backports. It cannot be removed until after libc++ has become stable enough to be the default, as some parts of gnustl are still incompatible with Clang. It will be removed when the other STLs are removed in r18.
        
        libc++ is out of beta and is now the preferred STL in the NDK. Starting in r17, libc++ is the default STL for CMake and standalone toolchains. If you manually selected a different STL, we strongly encourage you to move to libc++. For more details, see this blog post.
        
        Support for ARM5 (armeabi), MIPS, and MIPS64 are deprecated. They will no longer build by default with ndk-build, but are still buildable if they are explicitly named, and will be included by "all", "all32", and "all64". Support for each of these has been removed in r17. Both CMake and ndk-build will issue a warning if you target any of these ABIs.

To make it work with r15

I simply did the following steps

First download the ndk 15c

https://developer.android.com/ndk/downloads/older_releases.html#ndk-15c-downloads

    cd ~/Android/Sdk
    mv ndk-bundle ndk-bundle.orig
    mv ~/Downloads/android-ndk-r15c-linux-x86_64.zip .
    unzip android-ndk-r15c-linux-x86_64.zip
    ln -sf android-ndk-r15c ndk-bundle

Then from the parent directory of the project folder

    ./gradlew  tasks app:ndkLibsToJar app:ndkBuild app:assembleDebug --stacktrace
