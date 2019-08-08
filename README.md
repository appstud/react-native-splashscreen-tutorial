# Add a splash screen to a React Native app

I always struggle to remember the steps required in order to add a splash screen to a React Native app, and some tutorials out there are a bit outdated, so here is my take on the subject.

This tutorial will explain how to create a simple, seamless transition splash screen. No bells and whistles, only a simple logo centered on a plain background.  
I used react-native init rn_splashscreen_tutorial to instanciate my project, using React-Native 0.60+.

**Note**: if you are using Expo or Create React Native App you don't need to go through all of this, this tutorial is for people that have either ejected their project or created it the good old way, using `react-native init`.

Also note that I'm not an iOS nor Android developer, so some steps may surely be improved. I'll be happy to read how you would do it in the comments!


## Splash screen assets

For this tutorial we will use the React logo. We need three sizes, to better match the devices screen sizes (300px, 600px @x2, 900px @x3).  
You can get this images from github \<INSERT LINK\>.

\<INSERT REACT LOGO\>


## Install and configure react-native-splash-screen

- Install `react-native-splash-screen`
```
yarn add react-native-splash-screen
```
or
```
npm install react-native-splash-screen --save
```
depending on your packages manager of choice

*WARNING*: since React Native 0.60 we don't need to link the libraries anymore, so *don't* run react-native link. If you still are on 0.59 or before though, do run it: `react-native link react-native-splash-screen`

- Update cocoa pods (mandatory for React Native 0.60+ or if you are using pods on your project)
```
cd ios && pod install && cd ..
```

- Open `App.js`
- Add `import SplashScreen from 'react-native-splash-screen';` with the other imports
- Also import `{ useEffect }` from react
- Add a useEffect hook in the component that calls `Splascreen.hide()`

\<INSERT rn_image_01\>

Optional: 
- Add import `{ Platform }` from react-native
- Replace the line `<StatusBar barStyle="light-content" />` line by `{Platform.OS === 'ios' && <StatusBar barStyle="light-content" />}`

\<INSERT rn_image_02\>

This step really depends on what you want to do with the status bar.  
For the purpose of this tutorial I want to have a dark status bar on Android (I will define it in the Android code) but I want to keep the default behavior for iOS, thus the added condition.


## Add a splash screen to an iOS app

First, open the project in Xcode.   
Open a command line and from the project root run
```
open ios/rn_splashscreen_tutorial.xcworkspace
```

Note: Starting React-native 0.60, the iOS project uses cocoapods, so you have to open [project_name].xcworkspace and not [project_name].xcodeproj.


### Add the image assets to the project
In the left most navigator:
- select [project_name] > [project_name] > Imagex.xcassets 
- click the "+" icon in the second left navigator and select "New Image Set"

\<INSERT ios_image_01\>

- name your image set "SplashIcon"
- Add the three images you downloaded earlier. You can drag and drop all of them at the same time, Xcode will sort them by pixel density.

\<INSERT ios_image_02\>

### Change the background color
In the left most navigator:
- Open LaunchScreen.xib

\<INSERT ios_image_03\>

- Select the two elements, [project_name] and "Powered by React Native" and delete them
- In the second left navigator, click on "View"
- Then on the right navigator, click on the "Attribute inspector" icon (the one resembling a cursor or a small shield, 4th position)
- In the "Background" select list, select "Custom", that will popup a dialog
- Select the color you want (ProTip: you can enter an hexadecimal value in the options of the second tab), here we will set #424242

\<INSERT ios_image_04\>

### Add the icon to the screen

We now have to add the image we added to the project before.  
- In the top right of the Xcode window, click on the Library icon (the one with a square inside a circle)
- Select "Image view"

\<INSERT ios_image_05\>

- Make sure the Image View is a child of the View element, like in the next screenshot
- In the right navigator, select the SplashIcon asset from the "Image" select list
- Set the "Content Mode" option to "Aspect Fit" if it's not already the case

\<INSERT ios_image_06\>

### Center the image

We need to make sure the icon is centered regardless of the device the app is running on. To do that:
- Select the Image View in the second left navigator
- In the right navigator click on the "Size inspector" icon (the one that looks like a ruler, 5th one)
- In the autoresizing section disable the outer red lines and enable the inner arrows

\<INSERT ios_image_07\>

At this point we have our splash screen working, but you can notice a white screen flashing just before the content is loaded.  
What happens is that the splash screen is displayed by the native code, then the javascript part of the app is booted up while a white screen is briefly shown.  

\<INSERT ios_video_01\>

We also want the splash screen to be displayed during the React Native boot.
To do that we will use `react-native-splash-screen`.

### Configure react-native-splash-screen

- In Xcode, open the file [project_name] > [project_name] > AppDelegate.m
- Add `#import “RNSplashScreen.h"` with the other imports
- Add `[RNSplashScreen show];` just above `return YES;` in the `didFinishLaunchingWithOptions` method.

\<INSERT ios_image_08\>

### Change the status bar style
If you selected a dark color as the background of the splash screen, it would be better to have a light font instead of the current dark one, let's change that.

- In Xcode open [project_name] > [project_name] > Info.plist
- Right-click in the displayed list and select "Add row"
- Add a row. The key should be "Status Bar Style" and the value "UIStatusBarStyleLightContent"

\<INSERT ios_image_10\>

### Conclusion
You now should have a perfect seamless transition splash screen in your iOS app.

\<INSERT ios_video_02\>


## Add a splash screen to an Android app

Now the Android part!  
While for iOS we mostly clicked a lot through the Xcode interface, for Android we will directly create or edit code files . No need to use Android Studio but it's always good to have an IDE pointing out your mistakes in real time :)

### Add the image assets to the project

Android assets are located in `android/app/src/main/res`. There is a folder for each pixel densities.  
Add our splash screen logo to the folders following this mapping:
- mipmap-mdpi = splash_icon.png
- mipmap-hdpi = splash_icon@2x.png
- mipmap-xhdpi = splash_icon@3x.png
- mipmap-xxhdpi = splash_icon@3x.png
- mipmap-xxxhdpi = splash_icon@3x.png

And then rename all the files to `splash_icon.png`

### Create the splash screen
- Create a `background_splash.xml` file in `android/app/src/main/res/drawable` (create the drawable directory if it doesn't exist)
- Add the following code:
```
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">

    <item
        android:drawable="@color/splashscreen_bg"/>

    <item
        android:width="300dp"
        android:height="300dp"
        android:drawable="@mipmap/splash_icon"
        android:gravity="center" />

</layer-list>
```

This creates a list of layers composed of two items: a plain background and our icon.

- Create a `colors.xml` in `android/app/src/main/res/values` with the following contents:
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="splashscreen_bg">#424242</color>
    <color name="app_bg">#424242</color>
</resources>
```

It defines the color variables we just used, plus another one that will be used as the default background of the app if React Native doesn't draw anything else on top of it.

- Open `android/app/src/main/res/values/styles.xml` and replace the contents with:
```
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <!-- Customize your theme here. -->
        <item name="android:textColor">#000000</item>

        <!-- Add the following line to set the default status bar color for all the app. -->
        <item name="android:statusBarColor">@color/app_bg</item>
        <!-- Add the following line to set the default status bar text color for all the app 
        to be a light color (false) or a dark color (true) -->
        <item name="android:windowLightStatusBar">false</item>
        <!-- Add the following line to set the default background color for all the app. -->
        <item name="android:windowBackground">@color/app_bg</item>
    </style>

    <!-- Adds the splash screen definition -->
    <style name="SplashTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <item name="android:statusBarColor">@color/splashscreen_bg</item>
        <item name="android:windowBackground">@drawable/background_splash</item>
    </style>

</resources>
```

If you want to play with different colors for the status bar and the background, just create other color variables in the `colors.xml` file.
I also set the status bar colors here for all the app because it's more convenient, but you can also use the <StatusBar> component on the React Native side to control the status bar appearance on a per-screen basis.

### Tell the app to boot on the splash screen
- Open `android/app/src/main/AndroidManifest.xml` and modify the contents as follows:
```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
  package="com.rn_splashscreen_tutorial">

    <uses-permission android:name="android.permission.INTERNET" />

    <application
      android:name=".MainApplication"
      android:label="@string/app_name"
      android:icon="@mipmap/ic_launcher"
      android:roundIcon="@mipmap/ic_launcher_round"
      android:allowBackup="false"
      android:theme="@style/AppTheme">

        <!-- Add this SplashActivity -->
        <activity
          android:name=".SplashActivity"
          android:theme="@style/SplashTheme"
          android:label="@string/app_name">
          <intent-filter>
              <action android:name="android.intent.action.MAIN" />
              <category android:name="android.intent.category.LAUNCHER" />
          </intent-filter>
        </activity>

        <!-- Remove the intent-filter of the MainActivity and add a param android:exported="true" -->
        <activity
          android:name=".MainActivity"
          android:label="@string/app_name"
          android:configChanges="keyboard|keyboardHidden|orientation|screenSize"
          android:windowSoftInputMode="adjustResize"
          android:exported="true"/>

      <activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />
    </application>

</manifest>
```

Note: Don't forget to change the package name to match your own.

- Create a file `android/app/src/main/java/[your_package]/SplashActivity.java` with the contents:
```
package com.rn_splashscreen_tutorial; // Change this to your package name.

import android.content.Intent;
import android.os.Bundle;
import androidx.appcompat.app.AppCompatActivity;

public class SplashActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        Intent intent = new Intent(this, MainActivity.class);
        startActivity(intent);
        finish();
    }
}
```

We should now be able to run the android app and see the splash screen before the React Native part boots up:

\<INSERT android_video_01\>

You can notice that during the transition from the splash screen to the app a blank screen is displayed for a bit of time though.

Same solution than for iOS, we will use `react-native-splash-screen`, but Android need a bit more work to set it up.

### Install and configure react-native-splash-screen

- If you didn't install it before during the iOS setup, run
```
yarn add react-native-splash-screen
```
or
```
npm install react-native-splash-screen --save
```
depending on your packages manager of choice

*WARNING*: since React Native 0.60 we don't need to link the libraries anymore, so *don't* run react-native link. If you still are on 0.59 or before though, do run it: `react-native link react-native-splash-screen`

Then on the React Native side, if you didn't add it previously, open `App.js` and:
- Add `import SplashScreen from 'react-native-splash-screen';` with the other imports
- Also import `{ useEffect }` from react
- Add a useEffect hook in the component that calls `Splascreen.hide()`

\<INSERT ios_image_09\>

- In `android/app/src/main/java/[your_package]/MainActivity.java`, make these modifications:
```
package com.rn_splashscreen_tutorial; // This should be your package name.

import com.facebook.react.ReactActivity;
import org.devio.rn.splashscreen.SplashScreen; // Import this.
import android.os.Bundle; // Import this.

public class MainActivity extends ReactActivity {
    // Add this method.
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        SplashScreen.show(this);
        super.onCreate(savedInstanceState);
    }

    ...
}
```

Here we are telling Android to show our splash screen a bit longer during the MainActivity initialization with the use of the `react-native-splash-screen` library.

- Create a file `android/app/src/main/res/layout/launch_screen.xml` with the contents
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/splashscreen_bg"
    android:gravity="center">

  <ImageView
    android:layout_width="300dp"
    android:layout_height="300dp"
    android:layout_marginTop="24dp"
    android:src="@mipmap/splash_icon"
  />
</LinearLayout>
```

This is a second splash screen displayed by `react-native-splash-screen`.  
It's basically the same as the previous one we defined in `background_splash.xml`. Apparently we can't use the same one in the native part not managed by `react-native-splash-screen`.

Note: `android:layout_marginTop` is the height of the status bar. If we omit this you'll see a "jump" when the splash screens switch.

### Conclusion
You now should have a perfect seamless transition splash screen in your Android app.

\<INSERT android_video_02\>

## Thanks / disclaimer
Thanks to [Spencer Carli](https://medium.com/@spencer_carli) for [this tutorial](https://medium.com/handlebar-labs/how-to-add-a-splash-screen-to-a-react-native-app-ios-and-android-30a3cec835ae) that helped me a lot to set up my first splash screen and which this article is based on!
