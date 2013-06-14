## Contents
- [Introduction](#introduction)
- [Quick Start](#quick-start)
    - [Installation](#installation)
    - [Code](#code)
- [Basic Features](#basic-features)
    - [In-App-Purchase Count](#in-app-purchase-count)
    - [Custom Parameter](#custom-parameter)
    - [Event](#event)
- [Custom Banner](#custom-banner)
    - [Floating View](#floating-view)
    - [Banner View](#banner-view)
- [Push Notification](#push-notification)
    - [Custom Notification](#custom-notification)
- [Advanced Features](#advanced-features)
    - [AFLoadListener](#afloadlistener)
    - [AFShowListener](#afshowlistener)
    - [Custom URL](#custom-url)
    - [Test Device ID](#test-device-id)
    - [Timeout Interval](#timeout-interval)
- [Trouble Shooting](#trouble-shooting)
    - [Error Code](#error-code)
- [Release Notes](#release-notes)

* * *

## Introduction

The _AD fresca SDK_ provides a full size interstitial, which takes 80% of the the device screen except the upper status bar. _Android SDK_ is implemented as a simple View so developers can easily integrate our SDK into their application by following two steps: 1) SDK Installation and 2) Adding `AdFresca` into an app with a few lines of codes.

Unlike other SDKs by other AD networks, AD fresca SDK does not show content to users until the data is loaded and ready for display. Also, if an content request takes more than 5 seconds (default value: you can customize it easily,) SDK will quit the process and return the control to the app. These features ensure that users will not have any bad user experience with our content even if they suffer from a bad mobile network connection. (which happens a lot.) 

* * *

## Quick Start

### Installation

Download SDK at the following link.

[Android SDK Download](http://file.adfresca.com/distribution/sdk-for-Android-beta.zip) (v2.0.0-beta.1)

Copy **AdFresca.jar** and **adfresca_attr.xml** to **lib** and **res/values** repectively.

<img src="https://adfresca.zendesk.com/attachments/token/bja88u9zake4knm/?name=add_adfresca_jar_and_attr_xml.png" width="300"/>

- Right-click on your project and click **Properties**.
- Select **Java Build Path** and **Libraries** tab.
- Click **Add JARs** and select **lib/AdFresca.jar**.

<img src="https://adfresca.zendesk.com/attachments/token/ogcnzf3kmyzbcvg/?name=add_jar.png" width="600" />

You have to add _User Permission_ to **AndroidManifest.xml**. _Android SDK_ needs _User Permission_ to get network statuses and device id to match a campaign. The collected data is encrypted and only used for matching.

Add _User Permission_ like the following codes.

```xml
<?xml version="1.0" encoding="utf-8"?>

<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.adfresca.demo" android:versionName="1.0">
    <application android:icon="@drawable/icon" android:label="@string/app_name">
       <activity android:name=".DemoIntroActivity"android:label="@string/app_name"
        …………….
      </activity>

      <service android:name="org.openudid.OpenUDID_service">
        <intent-filter>
          <action android:name="org.openudid.GETUDID" />
        </intent-filter>
      </service>

      <!-- Add following codes if you need a push notification feature -->
      <activity android:name="com.adfresca.ads.AdFrescaPushActivity" />
        <receiver android:name="com.google.android.gcm.GCMBroadcastReceiver" android:permission="com.google.android.c2dm.permission.SEND">  
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="your_app_package" />
          </intent-filter>
      </receiver>
      <service android:name=".GCMIntentService" />  <!-- To handle GCM messages, you need to implement GCMIntentService class (See 'Push Notification' for detail  -->

   </application>

    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

    <!-- Add following codes if you need a push notification feature -->
    <permission android:name="your_app_pakcage.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="your_app_package.permission.C2D_MESSAGE" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />

</manifest>
```

### Code

You need only several lines of code to start a campaign like the following.

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);
  setContentView(R.layout.activity_intro);
  
  AdFresca.setApiKey(API_KEY);
  AdFresca adfresca = AdFresca.getInstance(this);
  adfresca.startSession();
  adfresca.load();
  adfresca.show();
}
```

`AdFresca.setApiKey(API_KEY);` Set API Key

`adfresca.startSession();` Start requesting the session logging. Please make sure that it is called once while your app is running.

`adfresca.load();` Load a campaign.

`adfresca.show();` Show the loaded campaign.

You can see the following if everything is correct.

<img src="https://adfresca.zendesk.com/attachments/token/zngvftbmcccyajk/?name=device-2013-03-18-133517.png" width="240" />
&nbsp;
<img src="https://adfresca.zendesk.com/attachments/token/phn4fcpvbi2damx/?name=device-2013-03-18-133443.png" height="240" />
* * *

## Basic Features

### In-App Purchase Count

If your app users use 'in-app-purchase', AD fresca can record this information on our database for your user targeting features.

If you want to use this feature, just call `AdFresca.setIsInAppPurchasedUser(int)`.

```java
  AdFresca adfresca = AdFresca.getInstance(this);
  AdFresca.setIsInAppPurchasedUser(User.getInAppPurchaseCount());
  adfresca.startSession();
  adfresca.load();
  adfresca.show();
```

### Custom Parameter

AD fresca can recored user specific information such as level, stage, gender and etc to use targeting and analytics features.

In SDK, you can just set custom parameters using setCustomParameter method with passing parameter's index and value.

(You can set and see the custom parameter's index in our Admin website: 1) Select App 2) In 'Overview' menu, click 'Details' button for each app store)

```java
  AdFresca adfresca = AdFresca.getInstance(this);
  
  adfresca.setCustomParameter(CUSTOM_PARAM_INDEX_LEVEL, User.level);
  adfresca.setCustomParameter(CUSTOM_PARAM_INDEX_AGE, User.age);
  adfresca.setCustomParameter(CUSTOM_PARAM_INDEX_HAS_FB_ACCOUNT, User.hasFacebookAccount);
  
  adfresca.startSession();
  adfresca.load();
  adfresca.show();
```

### Event

Using 'Event' feature, you can define your in-app events such as user's behavior, page navigation, and etc. Then you can target your campaigns for each event. 

For defining events on Dashboard, see ['Event Guide (Korean)'](https://adfresca.zendesk.com/entries/23359141)  

After you finished defining events, you need 'Event Index' value for each event. The index value is an integer value like '1,2,3,4'. We recommend to manage these values with Constant or Enum types in your source code.

To simply apply codes,  just pass event index into `AdFresca.load(int eventIndex)` method when the event occurs.

(If you don't specify event index on `AdFresca.load(int eventIndex)`, a default value is set to 1)

**Example**:  When user entered a main page

```java
  public class MainPageActivity extends Activity {
    public void onCreate(Bundle savedInstanceState) {
      AdFresca adfresca = AdFresca.getInstance(this);     
      adfresca.load(EVENT_INDEX_MAIN_PAGE);  // Request contents for main page event
      adfresca.show();
    }
  }
```

**Example**: When user's character level increased

```java
  public void onUserLevelChanged(int level) {
    AdFresca adfresca = AdFresca.getInstance(this);
    adfresca.setCustomParameter(CUSTOM_PARAM_INDEX_LEVEL, level); // 사용자 level 정보를 가장 최신으로 업데이트
    adfresca.load(EVENT_INDEX_LEVEL_UP);  // Request contents for level up event
    adfresca.show();
  }
```
* * *

## Custom Banner

_Android SDK_ provides with two kinds of _Custom Banner_ that makes it possible to show the images of various sizes. One is [Floating View](#floating-view) and the other is [Banner View](#banner-view).

[Floating View](#floating-view) is supposed to overlay other UI components and is closable by user interaction. On the other hand, [Banner View](#banner-view) is supposed to occupy the part of screen and is not closable.

In order to use _Custom Banner_, you have to add the namespace like the following.

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:adfresca="http://schemas.android.com/apk/res/Your.Package.Name"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
</LinearLayout>
```

_Android SDK_ matchs _Image Size Index_ of the loaded image with _Image Size Index_ of _Custom Banner_. If the image is matched, it is set to the matched _Custom Banner_. If not, it is not shown.

### Floating View

Add the following tag to layout xml to use _Floating View_.

```xml
<com.adfresca.sdk.view.AFFloatingView
    android:layout_width="match_parent"
    android:layout_height="80dp"
    adfresca:image_size_index="1" />
```

*   `adfresca:image_size_index=1` Set _Image Size Index_.

### Banner View

Add the following tag to layout xml to use _Banner View_.

```xml
<com.adfresca.sdk.view.AFBannerView
    android:layout_width="match_parent"
    android:layout_height="80dp"
    adfresca:default_image="@drawable/ic_launcher"
    adfresca:image_size_index="1"/>
```

*   `adfresca:image_size_index="1"` Set _Image Size Index_.
*   `adfresca:default_image="@drawable/default_image"` Set _Default Image_ that is displayed before the image is matched.

* * *


### Push Notification

You can send a push notification and see the result of how users respond a notification by AD fresca

Before you start, we recommend reading ["GCM: Getting Started" ](http://developer.android.com/google/gcm/gs.html) from Google.

1) Install GCM Helper Library
    - Download [GCM Helper Library](http://code.google.com/p/gcm/source/browse/) from Google. (Download zip or use 'git clone')
    - Import /gcm-client/dist/**gcm.jar** into your project
    
2) Add additional permissions and activities to AndroidManifest.xml.

```xml
<manifest>   
  <application>
      .........
      <activity android:name="com.adfresca.ads.AdFrescaPushActivity" />
      <receiver android:name="com.google.android.gcm.GCMBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">  
        <intent-filter>
          <action android:name="com.google.android.c2dm.intent.RECEIVE" />
          <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
          <category android:name="your_app_package" />
         </intent-filter>
      </receiver>
      <service android:name=".GCMIntentService" />  <!-- You must implement your own GCMIntentService class -->
   </application>
    ..........
    <permission android:name="your_app_pakcage.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="your_app_package.permission.C2D_MESSAGE" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    ..........
</manifest>
```

3) Get GCM device registration id and set it into SDK.

```java
    /*
    * GCM_SENDER_ID means Google API project number.
    * https://code.google.com/apis/console/#project:1234567890
    */
   final String GCM_SENDER_ID = "1234567890";
   
   GCMRegistrar.checkDevice(this);
   GCMRegistrar.checkManifest(this);
   
   final String gcmDeviceId = GCMRegistrar.getRegistrationId(this);  
   
   if (regId.equals("")) {
     GCMRegistrar.register(this, GCM_SENDER_ID);          
   }
   
  AdFresca adfresca = AdFresca.getInstance(this);
  adfresca.setPushRegistrationId(gcmDeviceId);
  adfresca.startSession();
```

4) Implement GCMIntentService class

```java
  public class GCMIntentService extends GCMBaseIntentService {

    /*
    * GCM_SENDER_ID means Google API project number.
    * https://code.google.com/apis/console/#project:1234567890
    */
    private static final String GCM_SENDER_ID = "1234567890";

    public GCMIntentService() {
      super(GCM_SENDER_ID);
    }

    @Override
    protected void onRegistered(Context context, String registrationId) {
     AdFresca.handlePushRegistration(registrationId);
   }

    @Override
    protected void onUnregistered(Context context, String registrationId) {
      AdFresca.handlePushRegistration(null);
    }

    @Override
    protected void onMessage(Context context, Intent intent) {

      // Check a push notification is form AD fresca.
      if (AdFresca.isFrescaNotification(intent)) { 
        String title = context.getString(R.string.app_name);
        int icon = R.drawable.icon;
        long when = System.currentTimeMillis();

        // Show this notification in the status bar
        // If this notification has URI Schema, SDK will open URI. otherwise, the activity from targetClass will be opened 
        AdFresca.showNotification(context, intent, MainActivity.class, title, icon, when);

      } 

    }

    @Override
    protected void onError(Context context, String registrationId) {

    }
  }
```

### Custom Notification

```java
public class GCMIntentService extends GCMBaseIntentService {
	@Override
	protected void onMessage(Context context, Intent intent) {
		if (AdFresca.isFrescaNotification(intent)) {
			String title = context.getString(R.string.app_name);
			int icon = R.drawable.icon;
			long when = System.currentTimeMillis();
			Notification notification = AdFresca.generateNotification(context, intent, DemoIntroActivity.class, title, icon, when);
			notification.sound = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
			NotificationManager notificationManager = (NotificationManager) context.getSystemService(Context.NOTIFICATION_SERVICE);
			notificationManager.notify(0, notification);
		}
	}
}
```
* * *
## Advanced Features

### AFLoadListener

_AFLoadListener_ makes it easier to update [Custom Parameter](#custom-parameter), [In-App-Purchase](#in-app-purchase). Because `AFLoadListener.onStart()` is always called when `AdFresca.load()` starts, you can easily update them by implementing _AFLoadListener_.

```
AdFresca.setLoadListener(new AFLoadListener(){
	@Override
	public void onStart() {
		Player player = ...;
		AdFresca.setCustomParameter(CUSTOM_PARAM_LEVEL, player.getLevel());
		
		int inAppPurchaseCount = ...;
		AdFresca.setInAppPurchaseCount(inAppPurchaseCount);
	}
});
```

### AFShowListener

_AFShowListener_ is called when a campaign is finished.

That, a campaign is finished, means the following two cases.

1. The campaign was shown and it has been closed.
2. A campaign was failed because it was expired or there was no [Custom Banner](#custom-banner) for its _Image Size Index_.

You can differentiate these two cases by `view` that is given by `AFShowListener.show(int eventIndex, AFView view)`

- if `view != null`, it was the first case. At this time, `view` is one of [ _Default View_ | _Floating View_ | _Banner View_ ]. (`view` is _Default View_ if `view.isDefaultView()` returns true.)
- if `view == null`, it was the second case.

```java
AdFresca adfresca = AdFresca.getInstance(this);
adfresca.startSession();
adfresca.load(EVENT_INDEX_STAGE_CLEAR);
adfresca.show(new AFShowListener(){
	@Override
	public void onFinish(int eventIndex, AFView view) {
		if(view == null) {
			// failed to show
		} else {
			if(view.isDefaultView()) {
				// shown on default view
			} else {
				// shown on floating view or banner view 
			}
		}
	}
});
```
**Example:** The following code show a campaign at _Intro Activity_ and will move to _Main Activity_ when it is finished.

```java
AdFresca adfresca = AdFresca.getInstance(this);
adfresca.startSession();
adfresca.load(EVENT_INDEX_INTRO);
adfresca.show(EVENT_INDEX_INTRO, new AFShowListener(){
	@Override
	public void onFinish(int eventIndex, AFView view) {
		startActivity(new Intent(IntroActivity.this, MainActivity.class));
	}
});
```

**_Caution!_**
If user clicked contents that opens Google Play or other applications, user will leave our of your app screen.

In this case, if you implemented onFinish() event like above example, user may see unnatural paging animation since app was temporally paused by other application.

To fix this issue, follow the steps below:

In admin dashboard, you should change 'Close mode' to 'Override' in your event settings. (it will prevent to close view when user clicked)
In app codes, override Activity's onResume() method like below:

```java
@Override
public void onResume() {
  super.onResume();

  AdFresca adfresca = AdFresca.getInstance(this);
  
  if (adfresca.getDefaultViewVisibility() == View.VISIBLE && adfresca.isUserClickedDefaultView()) {   
    adfresca.closeAd();
  }
}
```

### Custom URL

You can set your own URL Schema as _Click URL_ of AD fresca campaigns.

So, you can navigate your users to the specific page or do some custom actions when user clicked the content view.

To use this feature, add scheme in AndroidManifest.xml

```xml
  <activity android:name=".DemoZoneActivity">
      <intent-filter> 
             <action android:name="android.intent.action.VIEW" /> 
             <category android:name="android.intent.category.DEFAULT" /> 
             <category android:name="android.intent.category.BROWSABLE" /> 
             <data android:scheme="myapp" android:host="com.adfresca.zone" />
        </intent-filter> 
  </activity>
```

In this example above, DemoZoneActivity will be opened when you set _Click URL_ value as 'myapp://com.adfresca.zon' in your Campaign.

### Test Device ID

AD fresca supports a test mode feature. you can easily register test devices and manage them.

To check test device id, we provide two methods on our SDK.


1. Using getTestDeviceId() Method

```java
AdFresca adfresca = AdFresca.getInstance(this);
String deviceId = adfresca.getTestDeviceId();
textView.setText(deviceId);
```

2. Displaying test device id on your app screen using setPrintTestDeviceId method
 
```java
  AdFresca adfresca = AdFresca.getInstance(this);
  Log.d(TAG, "AD fresca Test Device ID is = " + adfresca.getTestDeviceId());
  adfresca.setPrintTestDeviceId(true);
  adfresca.load();
  adfresca.show();
```

### Timeout Interval

You can set a timeout interval for content request. If content is not loaded within this time interval, Content won't be displayed to users and SDK will return the control to your app.

Default is 5 seconds and you can set from 1 seconds to 5 seconds.

```java
  AdFresca adfresca = AdFresca.getInstance(this);
  AdFresca.setTimeoutInterval(5) // # 5 seconds
  adfresca.load();
  adfresca.show();
```

* * *

## Trouble Shooting

### Error Code

* * *

## Release Notes

- v2.0.0-beta.1 _(06/05/2013 Updated)_
    - Deprecated `AdFrescaView`. Use `AdFresca` instead.
    - Added [Custom Banner](#custom-banner)([Floating View](#floating-view), [Banner View](#banner-view)).
- v1.1.2
    - Added `setIsInAppPurchasedCount(int)` that makes it possible to manage user's In-App Purchase. (See [In-App Purchase](#in-app-purchase))
    - Deprecated `setIsInAppPurchasedUser(boolean)`. Use `setIsInAppPurchasedCount(int)` instead.
- v1.1.1
    - Ad is closed when user click the image.
    - Ad can be automatically closed. You can set it on dashboard.
    - Deprecated `onAdClicked`. It is recommended not to implement `onAdClicked`.
- v1.1.0
    - Added `Event Index`. It is set with `loadAd()`. See [Event Index](#event-index).
    - Deprecated `AD Slot`.
    - Display latest Ad. (It was not available to request while the previous request was in progress.
    - Added `onAdWillLoad` and `onAdClicked` to `AdListener`. You can close Ad when user click Ad.
- v1.0.1
    - SDK supports 'Push Notification' feature (See '9. Push Notification Setting' for detail)
- v1.0.0
    - The AD Caching feature is more optimized for better performance.
    - Bug fixed that user was not able to touch AD image when loadAd() and showAd() were called in different activities.
    - setPushRegistrationId() method is added. You can collect user's GCM ID to send a push notification in the near future. (More detailed guide will be available soon)
- v0.9.9 
    - SDK supports 'Custom Parameter' feature (See 'Custom Parameter Management')
- v0.9.8 
    - SDK Performance Improved
    - Bug fixed that onAdClosed() event was called twice after request timed out in specific condition.
- v0.9.7 
    - HTML5 View is added (There is no need to change any SDK code in your app!)
- v0.9.6 
    - closeAd() method is added. When a user touches 'back' button on the device, AD can be closed using closeAd() (See 'Adding AD view into App')
    - Bug fixed that there was a exception occurred in specific condition in loading AD.
    - 'AD fresca' logo is now located on the left of top bar.
- v0.9.5
    - AD Slot feature added as an announcement feature added (See 'AD Slot Setting')
    - getTestDeviceId(), setPrintTestDeviceId() methods are added to support a test mode (See 'Checking Test Device ID)
    - The AD Caching feature is optimized for better performance.
- v0.9.4 
    - Now, SDK use the AD Caching feature for faster ad display. If the cached AD exists, the cached AD will be shown up automatically.
    - AdFrescaView supports a shared object. Developers can use a single shared object and do not need to set API Key in different activities.
    - timeoutInterval property is added. You can set a timeout interval for AD request. If AD is not loaded within the time interval, AD won't be displayed to users.
    - testModeEnabled property is deprecated . All the test mode control will be proceed on our admin website from now on.
    - SDK supports Android 4.1.
- v0.9.3
    - setIsInAppPurchasedUser(boolean) method is added. You can manage your in-app purchased users with our service.
- v0.9.2
    - Library dependency error is solved
    - SDK is released with Google Gson and OpenUDID as a default. Some developers may get Duplicate Error if they already added them in project. A separated version will be released in the future. (Please contact us if you need it right a way)
    - 0.9.1
    - Bug fixed that timeout feature did not work correctly
- v0.9.0
    - AD fresca iOS SDK is now released! basic AD feature is included.