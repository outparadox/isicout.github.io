thumbnail: https://cdn-images-1.medium.com/max/2000/1*LgaStRUic1JjYfhdYplClg.jpeg
banner: https://cdn-images-1.medium.com/max/2000/1*LgaStRUic1JjYfhdYplClg.jpeg
---
title: Opencv In Android
date: 2017-05-06 15:17:39
tags: [Opencv, NDK, JNI, Android, C++]
---

this article is ready for a people  who has some knowledge on android develop and a newer about how use opencv SDK in android .

一: what is opencv 

简单的来说,  opencn 是跨平台的计算机视觉开源库, 它包含了何种图像识别算法

In simple terms , opencv is a open source library about Cross-platform computer vision 

[here is opencv ](https://developer.android.com/ndk/downloads/index.html?hl=zh-cn)

<!-- more -->

二: Setting up OpenCv Library inside Android 

1, first our develop environment

Android Studio 
Opencv SDK 3.2

2, you need download and import Opencv library SDK into android studio peoject 

[SDK](http://opencv.org/opencv-3-2.html)

然后你需要使用sdk的java 部分的代码作为你项目的moudle

 `From File -> New -> Import Module`, choose folder `OpenCV-android-sdk -> sdk -> java ` 

then add module dependency to your project

in android studio, `Application -> Module Settings` , select the Dependencies tab , click  `+` icon at bottom ,choose `Module Dependency` and select the have imported Opencv moudle 

it will looks like that :

![Opencv moudle](http://opd7g7we7.bkt.clouddn.com/WX20170508-162944.png)

copy libs folder under `sdk/native` to Android Studio under  `app/src/main` , and rename  `libs` to `jniLibs` , 

![Opencv libs](http://opd7g7we7.bkt.clouddn.com/opecv2.png)

3, use opencv library 

to use opencv, first you need to modify your Activity xml file . example  `activity_main.xml`

``` java
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:opencv="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <org.opencv.android.JavaCameraView
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:visibility="gone"
        android:id="@+id/tutorial1_activity_java_surface_view"
        opencv:show_fps="true"
        opencv:camera_id="any" />

</FrameLayout>
```

then modify your activity , load the `libopencv_java3.so` , in here , Note: for OpenCV version 3 at this step you should instead load the library `opencv_java3`.  OpenCV version 2, you should load the `opencv_java`



``` java
package com.example.huanjulu.opencvinandroidexample;

import org.opencv.android.BaseLoaderCallback;
import org.opencv.android.CameraBridgeViewBase.CvCameraViewFrame;
import org.opencv.android.LoaderCallbackInterface;
import org.opencv.android.OpenCVLoader;
import org.opencv.core.Mat;
import org.opencv.android.CameraBridgeViewBase;
import org.opencv.android.CameraBridgeViewBase.CvCameraViewListener2;

import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
import android.view.MenuItem;
import android.view.SurfaceView;
import android.view.WindowManager;

public class MainActivity extends Activity implements CvCameraViewListener2 {
    private static final String TAG = "OCVSample::Activity";

    private CameraBridgeViewBase mOpenCvCameraView;
    private boolean mIsJavaCamera = true;
    private MenuItem mItemSwitchCamera = null;

    private BaseLoaderCallback mLoaderCallback = new BaseLoaderCallback(this) {
        @Override
        public void onManagerConnected(int status) {
            switch (status) {
                case LoaderCallbackInterface.SUCCESS: {
                    Log.i(TAG, "OpenCV loaded successfully");
                    mOpenCvCameraView.enableView();
                }
                break;
                default: {
                    super.onManagerConnected(status);
                }
                break;
            }
        }
    };


    static {
        System.loadLibrary("opencv_java3");
    }
    /**
     * Called when the activity is first created.
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        Log.i(TAG, "called onCreate");
        super.onCreate(savedInstanceState);
        getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);

        setContentView(R.layout.activity_main);

        mOpenCvCameraView = (CameraBridgeViewBase) findViewById(R.id.tutorial1_activity_java_surface_view);

        mOpenCvCameraView.setVisibility(SurfaceView.VISIBLE);

        mOpenCvCameraView.setCvCameraViewListener(this);
    }

    @Override
    public void onPause() {
        super.onPause();
        if (mOpenCvCameraView != null)
            mOpenCvCameraView.disableView();
    }

    @Override
    public void onResume() {
        super.onResume();
        if (!OpenCVLoader.initDebug()) {
            Log.d(TAG, "Internal OpenCV library not found. Using OpenCV Manager for initialization");
            OpenCVLoader.initAsync(OpenCVLoader.OPENCV_VERSION_3_0_0, this, mLoaderCallback);
        } else {
            Log.d(TAG, "OpenCV library found inside package. Using it!");
            mLoaderCallback.onManagerConnected(LoaderCallbackInterface.SUCCESS);
        }
    }

    public void onDestroy() {
        super.onDestroy();
        if (mOpenCvCameraView != null)
            mOpenCvCameraView.disableView();
    }

    public void onCameraViewStarted(int width, int height) {
    }

    public void onCameraViewStopped() {
    }

    public Mat onCameraFrame(CvCameraViewFrame inputFrame) {
        return inputFrame.rgba();
    }

}

```

You can create the Main Activity copying the above code. 

First you would like notice is that `MainActivity` implements  `CvCameraViewListener2` interface , this interface woulf enfore us to implement few methods which are related to  the camera 

then 

``` java
public Mat onCameraFrame(CvCameraViewFrame inputFrame) {
        return inputFrame.rgba();
    }

```

这个是你使用opencv Java code 很重要的一个方法, 它接受相机的每帧原始数据, 并且你可以在这个方法内做所有的图片片处理工作, 它返回 `Mat `  作为相机要接受的帧数据.

it is the best importment method for you use opencv , this will receiver the vedio as frames and you can do all the image processing inside this method and return a Mat thie this image  :)


dont forget the permission of camera in your `AndroidMainfest.xml` 

``` java
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.huanjulu.opencvinandroidexample">
    <supports-screens android:resizeable="true"
        android:smallScreens="true"
        android:normalScreens="true"
        android:largeScreens="true"
        android:anyDensity="true" />

    <uses-sdk android:minSdkVersion="8" />

    <uses-permission android:name="android.permission.CAMERA"/>

    <uses-feature android:name="android.hardware.camera" android:required="false"/>
    <uses-feature android:name="android.hardware.camera.autofocus" android:required="false"/>
    <uses-feature android:name="android.hardware.camera.front" android:required="false"/>
    <uses-feature android:name="android.hardware.camera.front.autofocus" android:required="false"/>
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

when open this application , if you get the follow prompt :

It seems that you device does not support camera (or it is locked ). Application will be closed.

at this moment , you need manually turn on the camera Permission in device settting 

okay ,then yon can enjoy it 







