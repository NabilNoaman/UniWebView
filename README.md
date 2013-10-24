## UniWebView - An easier solution for integrating WebView to your mobile games

![UniWebView](http://uniwebview.onevcat.com/uniwebview-banner.png)

## What is UniWebView

**UniWebView** is a Unity3D package to help you using WebView easily on iOS and Android platform. You can set up a web view and embed web content in your game with less than 10 lines of code. There is also a clean and simple interface for you to interact between the game and webview. 

Main features:

* A native webview ([UIWebView](http://developer.apple.com/library/ios/#documentation/uikit/reference/UIWebView_Class/Reference/Reference.html) for iOS, [WebView](http://developer.android.com/reference/android/webkit/WebView.html) for Android). Compatible with html5, css3 and javascript.
* Send a message from webpage to Unity, using a specified url scheme. You can control the game flow and run your script code by clicking a url in the web page. By this strategy, you can implement a dynamic workflow to change your game logic on air.
* Excute and eval javascript defined in Unity game or webpage. 
* The size and apperence of webview is customizable.
* All source code of C# script and native plugin is included for a reference, with detailed documentation.
* Easy debug for Unity Mac Editor. There is no need to build and run again and again in your device. You can preview and interact with the webview just in editor. (Supporting for Windows Editor is on the way)

You can purchase `UniWebView` from [this link of Unity Asset Store](https://www.assetstore.unity3d.com/#/content/10116) and use it in all your projects with the purchase Unity ID.

## Getting Start

`UniWebView` is very easy to be integrated to your project. It just take you 3 steps to make it working. Just follow these:

1. Download and import the Unity package of `UniWebView` into your project. (If you already have an `AndroidManifest.xml`, you should uncheck the tick before this file when importing and follow [this brief merge instruction](http://)).
2. Drag and drop the `UniWebViewObject` prefab from `UniWebView/Prefab` folder to your game scene.
3. Change the `Url` in the Inspector to a url you what to load (or you can just play without changing anything, Unity3D's homepage will be get loaded) and Play your scene, your page should show automatically after loading.

Yes, now webpage is living in your game. The Prefab supplies a simple way to start using `UniWebView`. You can play with it in Unity Mac Editor, iOS devices or Android. The `Insets` could control the size of webview and `Load On Start` and `Auto Show When Load Complete` do exactly what they say. When you are ready, let's see how to **interact with the webview**.

## Communication bwtween webview and Unity game

The UniWebView would become USELESS if it is can just present a webpage. By using the package, you can easily talking to the webview from your game or receiving the message it sends out. You can follow the structures below to get some detail information.

### WebView to Unity

The suggested method is **using a url scheme to send message to Unity**. `UniWebView` will listen to a url starting with `uniwebview://`. When the url is loaded (regularly it is a click on the url link in your webpage by user), `UniWebView` will parse it to a `[UniWebViewMessage](http://uniwebview.onevcat.com/reference/struct_uni_web_view_message.html)` and then raise the `OnReceivedMessage` event. A `UniWebViewMessage` contains a `path` string to represent the path in the url, and a `args` dictionary to represent the parameters in the url. For example when the url 

A click on link with `uniwebview://move?direction=up&distance=1` will be parsed as

```
path = "move"
args = {
	direction = "up",
	distance = "1"
}
```

in a `UniWebViewMessage`, and you can implement your logic in a listener for `OnReceivedMessage`. There is a demo with step-by-step tutorial in the packeage to show how to use it. Check it whenever you want.

### Unity to WebView

You can run any javascript with the web page from Unity game. The javascript can be either a block of code embeded in the webpage, or a string of js code wrote in your game script. By using `[EvaluatingJavaScript](http://uniwebview.onevcat.com/reference/class_uni_web_view.html#a7cb377ca74716229fb995630db52d175)`, you can call and run javascript, so you can talk to your page to do something you want. As you may noticed, this method has a return value, you can return something you want by using it and get respond from the webpage.

There is some limitation here in evaluating a javascript. First is in the Mac Editor, the alert in the page would not pop up (But the js code will be excuted as expected). The second, there is a limitation on Android and the webview can not return value after evaluating the js. If you do need a return value for it, you can open a url start with "uniwebview://" and listen to the message event as a work around.

## Other Features

Besides of the basic web page and comunication, there are some other feature UniWebView for you.

* Background transparent - In iOS, there is a gray background by default in webview. You can use `SetTransparentBackground` to set the back ground transparent.
* Clean cache - The web view will keep the url request by default, which may cause an old page showed even if you update your web page. Use `CleanCache` to solve this problem.
* Go back and go forward - Just behaves as a browser, you can control the webpage navigation in your game, by `GoBack` and `GoForward` method.
* Multiple webviews. If you want more than one webview in your game, just instantiate another gameObject and add the script to it.

### Merge AndroidManifest.xml

If you already have an AndroidManifest.xml file in your project, you should not import the UniWebView's and add or update something to your old AndroidManifest.xml yourself. Don't worry about it, it's fairly simple. After you importing the package to your project without AndroidManifest.xml, you could follow these steps to make it work:

1. Open your AndroidManifest.xml file with any text editor you like. The file should be loacated in Assets/Plugins/Android.
2. Search for `UnityPlayerNativeActivity` in your AndroidManifest.xml. Regularly, there should be only 1 or no match in the file. It you found it in your file, follow Step 3.(1). Else if there is no match, follow Step 3.(2).
3. (1) Add the two entries below in the activity you found in Step 2, between the `<activity>` and `</activity>` tag. If they are already there and having the same value, you can leave them there and take a beer. If the value is not proper, you should change them to the same as below.

```xml
<meta-data android:name="android.app.lib_name" android:value="unity" />
<meta-data android:name="unityplayer.ForwardNativeEventsToDalvik" android:value="true" />
```
3. (2) If you did not find in in Step 2, you should add a default activity to the file. Copy code below and paste it to your AndroidManifest.xml, between the `<application>` and `</application>` tag

```xml
<activity android:name="com.unity3d.player.UnityPlayerNativeActivity"
                  android:label="@string/app_name"
                  android:configChanges="fontScale|keyboard|keyboardHidden|locale|mnc|mcc|navigation|orientation|screenLayout|screenSize|smallestScreenSize|uiMode|touchscreen">
            <meta-data android:name="android.app.lib_name" android:value="unity" />
            <meta-data android:name="unityplayer.ForwardNativeEventsToDalvik" android:value="true" />
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
```

Now you should have done it. You can [go back to the Getting Start](http://) or just continue to play with the plugin as you like.

## Demo Example

There is a simple demo to help you get started. Open the DemoScene under UniWebView/Demo folder to play with it. In that demo, you can get a basic idea how UniWebView works between native webview and your Unity game. You can follow the UniWebDemo.cs to setup your webview by code. And of course don't forget we have a UniWebViewObject in UniWebView/Prefab folder for you to quickly start without any other effort.

## Script Reference & Support Forum

You can find the [script reference here](http://uniwebview.onevcat.com/reference). There is also a [support forum](https://groups.google.com/forum/#!forum/uniwebview) for you to ask anything about `UniWebView`. You can also [submit an issue](https://github.com/onevcat/UniWebView/issues) if you encountered anything wrong. Once confirmed, I will fix them as soon as possible. Hope `UniWebView` can accelerate your development progress. [Get it](http://) now, enjoy it and have a good day :)
