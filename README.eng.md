## Contents
- [Introduction](#introduction)
- [Quick Start](#quick-start)
    - [Installation](#installation)
    - [Code](#code)
- [Basic Features](#basic-features)
    - [In-App-Purchase Count](#in-app-purchase-count)
    - [Custom Parameter](#custom-parameter)
    - [Event](#event)
- [Push Notification](#push-notification)
    - [Custom Notification](#custom-notification)
- [Custom URL](#custom-url)
- [Reward Item](#reward-item)
- [Custom Banner](#custom-banner)
    - [Floating View](#floating-view)
    - [Banner View](#banner-view)
- [Advanced Features](#advanced-features)
    - [AFLoadListener](#afloadlistener)
    - [AFShowListener](#afshowlistener)
    - [Custom URL](#custom-url)
    - [Test Device ID](#test-device-id)
    - [Timeout Interval](#timeout-interval)
    - [Google Referrer Tracking](#google-referrer-tracking)
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

[Android SDK Download](http://file.adfresca.com/distribution/sdk-for-Android.zip) (v2.3.1)

[Android SDK Download without Gson Library](http://file.adfresca.com/distribution/sdk-for-Android-wihtout-gson.zip) (v2.3.1)

Copy **AdFresca.jar** and **adfresca_attr.xml** to **lib** and **res/values** repectively.

<img src="https://adfresca.zendesk.com/attachments/token/bja88u9zake4knm/?name=add_adfresca_jar_and_attr_xml.png" width="300"/>

- Right-click on your project and click **Properties**.
- Select **Java Build Path** and **Libraries** tab.
- Click **Add JARs** and select **lib/AdFresca.jar**.

<img src="https://adfresca.zendesk.com/attachments/token/ogcnzf3kmyzbcvg/?name=add_jar.png" width="600" />

You have to add _User Permission_ to **AndroidManifest.xml**. _Android SDK_ needs _User Permission_ to get network statuses and device id to match a content. The collected data is encrypted and only used for matching.

Add _User Permission_ like the following codes.

```xml
<?xml version="1.0" encoding="utf-8"?>

<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.adfresca.demo" android:versionName="1.0">
    <application android:icon="@drawable/icon" android:label="@string/app_name">
       <activity android:name=".DemoIntroActivity"android:label="@string/app_name"
        …………….
      </activity>

      <!-- Service for OpenUDID -->
      <service android:name="org.openudid.OpenUDID_service">
        <intent-filter>
          <action android:name="org.openudid.GETUDID" />
        </intent-filter>
      </service>

      <!-- Activity for Incentivized Campaign -->
      <activity android:name="com.adfresca.sdk.reward.AFRewardActivity" />
      
       <!-- Boradcast Receiver for Google Referrer Tracking-->
      <receiver android:name="com.adfresca.sdk.referer.AFRefererReciever" android:exported="true">
      	<intent-filter>
            <action android:name="com.android.vending.INSTALL_REFERRER" />
     	</intent-filter>	
      </receiver>
            
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

`adfresca.load();` Load a content.

`adfresca.show();` Show the loaded content.

You can see the following if everything is correct.

<img src="https://adfresca.zendesk.com/attachments/token/zngvftbmcccyajk/?name=device-2013-03-18-133517.png" width="240" />
&nbsp;
<img src="https://adfresca.zendesk.com/attachments/token/phn4fcpvbi2damx/?name=device-2013-03-18-133443.png" height="240" />
* * *

## Basic Features

### In-App Purchase Count

If user purchases in-app items, you can save this information on our service to use use targeting and analytics features.

You can simply set a number of in-app purchases for current user, using setNumberOfInAppPurchases(int) method.

```java
  AdFresca adfresca = AdFresca.getInstance(this);
  
  public void onCreate() {
    AdFresca adfresca = AdFresca.getInstance(this);     
    adfresca.setNumberOfInAppPurchases(User.inAppPurchaseCount);
    adfresca.startSession();
  }
  
  .....
  
  public void onUserPurchasedItem() {
    User.inAppPurchaseCount++;
    
    AdFresca adfresca = AdFresca.getInstance(this);     
    adfresca.setNumberOfInAppPurchases(User.inAppPurchaseCount);
    adfresca.load(EVENT_INDEX_PURCHASE);
    adfresca.show();
  }
```

**Caution:** setNumberOfInAppPurchases() method should be called earlier than startSession() and load() as an example above. If this method is not initially called earlier than startSession(), the value of in-app purchase count is not updated to our service on user's first session, but the cached value is updated since user's second session (SDK caches the value on the local storage since v2.2.2)

(Advanced) You can check in-app purchase count with getNumberOfInAppPurchases() method. Also, if you want to reset in-app purchase count for some reason, you can call resetNumberOfInAppPurchases() method. Be careful not to call this method more than once.

### Custom Parameter

AD fresca can save user specific information such as level, stage, gender and etc to use targeting and analytics features.

In SDK, you can just set custom parameters using setCustomParameter method with passing parameter's index and value.

(You can set and see the custom parameter's index in our Admin website: 1) Select App 2) In 'Overview' menu, click 'Details' button for each app store)

```java
  public void onCreate() {
    AdFresca adfresca = AdFresca.getInstance(this);     
    adfresca.setCustomParameterValue(CUSTOM_PARAM_INDEX_LEVEL, User.level);
    adfresca.setCustomParameterValue(CUSTOM_PARAM_INDEX_AGE, User.age);
    adfresca.setCustomParameterValue(CUSTOM_PARAM_INDEX_HAS_FB_ACCOUNT, User.hasFacebookAccount);
    adfresca.startSession();
  }
  
  .....
  
  public void onUserLevelChanged(int level) {
    User.level = level
    
    AdFresca adfresca = AdFresca.getInstance(this);     
    adfresca.setCustomParameterValue(CUSTOM_PARAM_INDEX_LEVEL, User.level);
    adfresca.load(EVENT_INDEX_LEVEL_UP);
    adfresca.show();
  }
```

**Caution:** setCustomParameterValue() method should be called earlier than startSession() and load(). Especially, you should Initialize custom parameter values before calling startSession(), and then set the changed values at later events as an example above. However, some apps may not able to Initialize values earlier than startSession(). In this case, the values of custom parameters are not updated to our service on user's first session, but the cached values are updated since user's second session (SDK caches the value on the local storage since v2.2.2)

(Advanced) You can check the latest custom parameters with getCustomParameterValue(index) method. Also, if you want to reset custom parameters for some reason, you can call resetCustomParameterValues() method. Be careful not to call this method more than once.

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
    AdFresca.setCustomParameter(CUSTOM_PARAM_INDEX_LEVEL, level); // Update the latest user level
    adfresca.load(EVENT_INDEX_LEVEL_UP);  // Request contents for level up event
    adfresca.show();
  }
```
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
      <service android:name=".GCMIntentService" />  <!-- You must implement your own GCMIntentService class -->
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

  AdFresca.setPushRegistrationId(gcmDeviceId);
  
  AdFresca adfresca = AdFresca.getInstance(this);
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
        String appName = context.getString(R.string.app_name);
        int icon = R.drawable.icon;
        long when = System.currentTimeMillis();

        // Show this notification in the status bar
        // If this notification has URI Schema, SDK will open URI. otherwise, the activity from targetClass will be opened 
        AdFresca.showNotification(context, intent, MainActivity.class, appName, icon, when);
      }
    }

    @Override
    protected void onError(Context context, String registrationId) {
    }
}
```

5) Implement GCMReceiver class (Optional)

If GCMReceiver class is not located on the root package of your application, you should manually implement GCMReceiver class to indicate the location of GCMIntentService class. Create a new class named GCMReceiver and implement getGCMIntentServiceClassName method as below.

```java
public class GCMReceiver extends GCMBroadcastReceiver { 
   	@Override
	protected String getGCMIntentServiceClassName(Context context) { 
		return "your_app_package.GCMIntentService"; 
	} 
}
```

Then update AndroidMenefest.xml as below.

``` xml
....
<receiver android:name="your_gcm_package.GCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">  
  <intent-filter>
    <action android:name="com.google.android.c2dm.intent.RECEIVE" />
    <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
    <category android:name="your_app_package" />
  </intent-filter>
</receiver>
<service android:name="your_gcm_package.GCMIntentService" /> 
....
```

### Custom Notification

You can change the settings of _Notification_. These examples below show how to do that.

**Example**: Add default sound, vibration, and light when notification is shown on device.

```java
public class GCMIntentService extends GCMBaseIntentService {
	@Override
	protected void onMessage(Context context, Intent intent) {
		if (AdFresca.isFrescaNotification(intent)) {
			String appName = context.getString(R.string.app_name);
			int icon = R.drawable.icon;
			long when = System.currentTimeMillis();
						
			AFPushNotification notification = AdFresca.generateAFPushNotification(context, intent, DemoIntroActivity.class, appName, icon, when);
			notification.setDefaults(Notification.DEFAULT_ALL); // requires VIBRATE permission
			AdFresca.showNotification(notification);
		}
	}
}
```

To support 'vibrate' mode, you should also add a permission to AndroidManifest.xml
```xml
<uses-permission android:name="android.permission.VIBRATE"></uses-permission>
```

**Example**: Use BigTextStyle with a custom notificiation to show messages.

```java
public class GCMIntentService extends GCMBaseIntentService {
	@Override
	protected void onMessage(Context context, Intent intent) {
		if (AdFresca.isFrescaNotification(intent)) {
	            String appName = context.getString(R.string.app_name);
	            int icon = R.drawable.icon;
	            long when = System.currentTimeMillis();
				
	            AFPushNotification notification = AdFresca.generateAFPushNotification(context, intent, DemoIntroActivity.class, appName, icon, when);

	            Notification.Builder builder =
	                    new Notification.Builder(this)
	                            .setSmallIcon(icon)
	                            .setContentTitle(notification.getTitle())
	                            .setContentText(notification.getMessage())
	                            .setTicker(notification.getTickerText())
	                            .setDefaults(Notification.DEFAULT_ALL) // requires VIBRATE permission
	                            .setContentIntent(notification.getContentIntent())
	                            .setAutoCancel(notification.isAutoCancelled())
	                            /*
	                             * Big view style is only supported on 4.1+ devices.
	                             */
	                            .setStyle(new Notification.BigTextStyle()
	                                .bigText(notification.getMessage())
	                            );
	
	            NotificationManager notificationManager = (NotificationManager) context.getSystemService(Context.NOTIFICATION_SERVICE);
	            notificationManager.notify(0, builder.build());
		}
	}
}
```
*Caution:* Be careful not to change 'notification.contentIntent' object. You should should use the one fetched from generateNotification() method. 
* * *

## Custom URL

You can set your own URL Schema for click url of Announcement Campaign and url schema of Push Notification Campaign. 

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

In this example, you can set url as yapp://com.adfresca.zone?item=abc to open DemoZoneActivity when user responds.

on DemoZoneActivity, you can get parameter values (item=abc) as example codes below.

```java
public void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);

  Uri uri = getIntent().getData();
  if (uri != null && uri.getScheme().equals("myapp")) { 
    String item = uri.getQueryParameter("item");
  }
}
```

### Using Custom URL on Coscos2d-x

Unlike the native android application that uses multiple activities as its pages, coscos2d-x and Unity engine use only one activity and implements engine's own paginations internally. So, there is a problem to add schema information since you cannot set schema on 'MAIN' launcher activity.

To solve this issue, you need to do some extra works as below.

1) Override startActivity(intent) of Main Activity to handle Custom URL for Announcement Campaign.

Click URL from Announcement Campaign is always executed on in-game situation. It is never executed from outside of game like a push notification. Also, SDK uses startActivity() method to execute url. Therefore, you can manually handle urls by overriding startActivity(). The code below shows how to handle custom url with 'myapp://' schema, and it does not open a new activity for custom url 

```java
\@Override 
public void startActivity(Intent intent) { 
  boolean isStartActivity = true;

  // Check intent 
  Uri uri = intent.getData(); 
  if (uri != null && uri.getScheme().equals("myapp")) { 
    isStartActivity = false; 
  }

  if (isStartActivity) { 
    super.startActivity(intent); 
  } else { 
    // Log.d("TEST", "MainActivity.startActivity() : uri = " + uri.toString());   
    // Do something with uri
  } 
}
```
(In this case, you don't need to add any schema information in AndroidMenefest.xml)

2) Handle custom url form Push Notification Campaign

When your app receive a push notification with custom url, you can execute your own custom action. A notification is mostly received when user is outside of game. So, we should handle custom url with a little bit different approach. 

Firstly, create a new activity class named 'PushProxyActivity', and register the activity in AndroidMenefest.xml as below

```xml
<activity android:name=".PushProxyActivity">
	<intent-filter> 
 		<action android:name="android.intent.action.VIEW" /> 
		<category android:name="android.intent.category.DEFAULT" /> 
		<category android:name="android.intent.category.BROWSABLE" /> 
		<data android:scheme="myapp" android:host="com.adfresca.push" />
	</intent-filter> 
</activity>

.......

<uses-permission  android:name="android.permission.GET_TASKS"/>
```
In this case, you should create custom url like myapp://com.adfresca.push?item=abc in your Push Notification Campaign. 

Then, you should implement PushProxyActivity class. This class is a simple proxy-style activity which only handle url form Android OS and then quit itself. 
However, there is a exceptional situation when notification is received and your application is not running. In that case, you can't handle custom url in the game engine, so you should manually start an application and pass urls as parameters as below.

```java
public class PushProxyActivity extends Activity {
  @Override
  public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    
    // hide ui
    requestWindowFeature(Window.FEATURE_NO_TITLE);
    getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,WindowManager.LayoutParams.FLAG_FULLSCREEN);
    getWindow().setBackgroundDrawableResource(android.R.color.transparent);

    Uri uri = getIntent().getData();
    if (uri != null) {
      if (isActivityRunning()) {
        // Log.d("AdFresca", "PushProxyActivity.onCreate() with isActivityRunning : url = " + uri.toString());
        // Do something with uri
   	
     } else {
       // Log.d("AdFresca", "PushProxyActivity.onCreate() wihtout isActivityRunning :  uri = " + uri.toString());
       
       // Run a new cocos2dx activity with uri
       Intent intent = new Intent(this, SimpleGame.class);
       intent.putExtra(Constant.FRESCA_URL_KEY, uri.toString());
       intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP | Intent.FLAG_ACTIVITY_SINGLE_TOP);
       startActivity(intent);
     }   			
   }
               
    finish();
  }
  
  private boolean isActivityRunning() { 
    ActivityManager activityManager = (ActivityManager)this.getSystemService (Context.ACTIVITY_SERVICE); 
    List<RunningTaskInfo> activitys = activityManager.getRunningTasks(Integer.MAX_VALUE); 
    boolean isActivityFound = false; 
    String activityInfo = "ComponentInfo{YOUR_PACKAGE/YOUR_PACKAGE.GAME_ACTIVITY_NAME}" // "ComponentInfo{org.cocos2dx.simplegame/org.cocos2dx.simplegame.SimpleGame}"
    for (int i = 0; i < activitys.size(); i++) { 
      if (activitys.get(i).topActivity.toString().equalsIgnoreCase(activityInfo)) {
        isActivityFound = true;
        break;
      }
    } 
    return isActivityFound; 
  } 
}
```

Finally, you should handle url from PushProxyActivity on your Main activity.

```java
  @Override
  public void onCreate(Bundle savedInstanceState) {
    ......
    // Handle custom uri from PushProxcyActivity
    String frescaURL = this.getIntent().getStringExtra(Constant.FRESCA_URL_KEY);
    if (frescaURL != null) {
      // Log.d("AdFresca", "MainActivity.onCreate() with uri = " + frescaURL);  
      // Do something with uri
    } 	
    ......
  }
```

Now, you're done with custom url in coscos2d-x engine!
***

## Reward Item

_Incentivzed Campaign_ makes it possible to give a reward to users who see an ad of _Advertising App_ in _Media App_ and install _AdVertising App_.

(It is highly recommended to install SDK in _Advertising App_ for _CPA Campaign_ that is coming soon although it is not required at this time.)

Code in _Media App_:

- Add `<activity>` tag to AndroidManifest.xml

```xml
<manifest>   
  <application>
      .........
      <activity android:name="com.adfresca.sdk.reward.AFRewardActivity" />
      .........
   </application>
</manifest>
```

- Call getAvailableRewardItems() method when you want to get item list.

- Array returned from getAvailableRewardItems() contains objects of 'AFRewardItem' which has name, quantity and uniqueValue properties. You should use uniqueValue property of AFRewardItem object to give an item to your users. (ex: you may send an item request to your game server with user id and uniqueValue)

- 'getAvailableRewardItems()' method only return when all the validation work are done for current user. it may return items at later if reward is still in validation process.

```java
@Override
public void onStart() {
  super.onStart();

  AdFresca adfresca = AdFresca.getInstance(DemoIntroActivity.this);
  List<AFRewardItem> items = adfresca.getAvailableRewardItems();

  if (items.size() > 0) {
    for (AFRewardItem item : items) {
      Log.d(TAG,String.format("Get AFRewardItem: name=%s, quantity=%d, uniqueVlaue=%s", item.getName(), item.getQuantity(), item.getUniqueValue())));
      // do something with tem.getName(), item.getQuantity(), item.getUniqueValue()
    }
    String itemNames = joinNameStringsByComma(items);
    String alertMessage = String.format("You got the reward item(s)! (%s)", itemNames);
    showAlert(alertMessage);
  }
}
```

###(Advanced) Giving a reward with faster way:

- Call `checkRewardItems()` to check at the time of starting app or whenever you want.
- Call `getAvailableRewardItems()` to retrieve available reward items to give it to users.

```java
@Override
public void onStart() {
  super.onStart();

  AdFresca adfresca = AdFresca.getInstance(DemoIntroActivity.this);
  adfresca.checkRewardItems();
}

@Override
public void onClick(View view) {
  List<AFRewardItem> items = adfresca.getAvailableRewardItems();

  if (items.size() > 0) {
    for (AFRewardItem item : items) {
      Log.d(TAG,String.format("Get AFRewardItem: name=%s, quantity=%d, uniqueVlaue=%s", item.getName(), item.getQuantity(), item.getUniqueValue())));
      // do something with tem.getName(), item.getQuantity(), item.getUniqueValue()
    }
    String itemNames = joinNameStringsByComma(items);
    String alertMessage = String.format("You got the reward item(s)! (%s)", itemNames);
    showAlert(alertMessage);
  }
}
```

###(Advanced) Giving a reward immediately:

`checkRewardItems(true)` blocks your code flow. That `checkRewardItems(true)` returns means it is finished to check available reward item. After it returns, you may call `getAvailableRewardItems()` to give users reward items.


```java
new AsyncTask<Void, Void, Void>() {
  protected Void doInBackground(Void... params) {
    AdFresca adfresca = AdFresca.getInstance(DemoIntroActivity.this);
    adfresca.checkRewardItems(true);
    return null;
  }

  protected void onPostExecute(Void param) {
    AdFresca adfresca = AdFresca.getInstance(DemoIntroActivity.this);
    List<AFRewardItem> items = adfresca.getAvailableRewardItems();

    if (items.size() > 0) {
      for (AFRewardItem item : items) {
      Log.d(TAG,String.format("Get AFRewardItem: name=%s, quantity=%d, uniqueVlaue=%s", item.getName(), item.getQuantity(), item.getUniqueValue())));
      // do something with tem.getName(), item.getQuantity(), item.getUniqueValue()
      }
      String itemNames = joinNameStringsByComma(items);
      String alertMessage = String.format("You got the reward item(s)! (%s)", itemNames);
      showAlert(alertMessage);
    }
  }
}.execute();
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

You can set _close\_button\_image_ for users to be able to close _Floating View_.

```xml
<com.adfresca.sdk.view.AFFloatingView
    android:layout_width="match_parent"
    android:layout_height="80dp"
    adfresca:image_size_index="1"
    adfresca:close_button_image="@drawable/close_button" />
```

*   `adfresca:close_button_image="@drawable/close_button"` Set an image of close button.

### Banner View

Add the following tag to layout xml to use _Banner View_.

```xml
<com.adfresca.sdk.view.AFBannerView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    adfresca:image_size_index="1"
    adfresca:keep_aspect_ratio="width"
    adfresca:default_image="@drawable/default_banner" />
```

- `adfresca:image_size_index="1"` Set _Image Size Index_.
- `adfresca:keep_aspect_ratio="width"` Keep _Banner View_'s aspect ratio along with  the loaded content. If it is set to _width_, _Banner View_'s height will be changed to keep aspect ratio. In this case, `android:layout_height` must be `wrap_content`. (`adfresca:keep_aspect_ratio` is set to [ _none_ | _width_ | _height_ ]. _none_ is a default.)
- `adfresca:default_image="@drawable/default_image"` Set _Default Image_ that is displayed before the image is matched.

**Example:** Using _Default View(Interstitial View)_ and _Banner View_ in a activity.

You are able to show a number of views by [Event](#event).

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);
  setContentView(R.layout.activity_intro);
  
  AdFresca.setApiKey(API_KEY);
  AdFresca adfresca = AdFresca.getInstance(this);
  adfresca.startSession();
  adfresca.load(EVENT_INDEX_MAIN_PAGE_FOR_BANNER); // load the content for Banner View of Main page
  adfresca.load(EVENT_INDEX_MAIN_PAGE_FOR_INTERSTITIAL); // load the content for Interstitial View of Main page
  adfresca.show(); // show all loaded contents.
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

1. The content was shown and it has been closed.
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
**Example:** The following code show a content at _Intro Activity_ and will move to _Main Activity_ when it is finished.

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

### Google Referrer Tracking

You can see how many users installed your app through Google Play Campaign by tracing referrer information.

To fetch referrer and set it to our SDK, you can add a receiver and do test below.

1) Register Receiver to AndroidManefest.xml

By registering this receiver, our SDK will automatically collects referrer information and update to AD fresca server. 

```xml
<receiver android:name="com.adfresca.sdk.referer.AFRefererReciever" android:exported="true">
	<intent-filter>
      		<action android:name="com.android.vending.INSTALL_REFERRER" />
     	</intent-filter>
</receiver>
```

2) Test referrer using ADB

After connecting Android device to your computer, open adb shell (adb shell is located platform-tools directory inside of installed Android SDK directory).

Then you can manually send INSTALL_REFERRER message to your device. Just change package name and referrer values below.
(You can see detailed information of each referrer value in [Google Play - Campaign Parameters](https://developers.google.com/analytics/devguides/collection/android/v2/campaigns#campaign-params))

```sh
am broadcast -a com.android.vending.INSTALL_REFERRER -n YOUR_PACKAGE/com.adfresca.sdk.referer.AFRefererReciever --es "referrer" "utm_source=test_source&utm_medium=test_medium&utm_term=test_term&utm_content=test_content&utm_campaign=test_name"
```
3) Check if referrer was successfully set on SDK

```java
  AdFresca adfresca = AdFresca.getInstance(this);
  Log.v(TAG, "Google Referrer = " + adfresca.getReferrer());
``` 
(Advanced) If you already uses another broadcast receiver to handle INSTALL_REFERRER, you can manually set your referrer by calling setReferrer(string) method

Caution: After your device updates referrer once to our service, it won't be able to change the referrer values for this device.
* * *

## Trouble Shooting

if you cannot see our content or get other errors, you can debug by implementing AFExceptionListener.

```java
AdFresca.setExceptionListener(new AFExceptionListener(){
  @Override
  public void onExceptionCaught(AFException e) {
    Log.w("TAG", e.getCode() + ":" + e.getLocalizedMessage());
  }
});
```

### Error Code

Code | Message | Description
------------ | ------------- | ------------
UNKNOWN_ERROR = 1 | Unknown Error | Unknown error has occurred. Please contact our dev team to solve this issue.
NO_APIKEY = 1 | No API Key set to AdFresca | There is no API Key set to AdFresca.
NO_ACTIVITY = 2 | No Activity set to AdFresca | There is no Activity set to AdFrescaView.
NO_INTERNET_CONNECTION = 3 | No Internet Connection | The network connection is not available. android.permission.INTERNET permission might not be added.
NO_DEVICE_ID = 4 |  | SDK can't get a device id. OpenUDID might not be added.
NO_NETWORK_STATUS = 5 |  | SDK can't firgure out a network status of device. ACCESS_NETWORK_STATE permission might not be added.
INVALID_ADREQEST_IMPRESSION = 6 | We're sorry, but something went wrong. /impression | An error has occurred on our server. Please contact our dev team to solve this issue.
INVALID_ADREQEST_IMAGE = 7 | We're sorry, but something went wrong. /image | An error has occurred on our server. Please contact our dev team to solve this issue.
INVALID_ADREQEST_DISPLAYED = 8 | We're sorry, but something went wrong. /displayed | An error has occurred on our server. Please contact our dev team to solve this issue.
INVALID_ADREQEST_CLICKED = 9 | We're sorry, but something went wrong. /clicked | An error has occurred on our server. Please contact our dev team to solve this issue.
AD_TIMEOUT = 10 | Request Timed Out | Content has not been loaded after timeout interval. It usually occurs when the device has a week network connection.
NO_APP_VERSION = 11 | No app version in manifest.xml | There is no versionName set in manifest.xml
INVALID_SESSION_REQUEST = 12 | We're sorry, but something went wrong. /session | An error has occurred on our server. Please contact our dev team to solve this issue.
INVALID_ADREQEST_ACTIVE = 13 | We're sorry, but something went wrong. /active | An error has occurred on our server. Please contact our dev team to solve this issue.
AD_IMPRESSION_EXPIRED = 14 | This AD is expired | Your content that you loaded has been expired and not able show to users. Try load again.
INVALID_PUSH_RUN_REQUEST = 15 | We're sorry, but something went wrong. /push_notification | An error has occurred on our server. Please contact our dev team to solve this issue.
UNKNOWN_REQUEST_TYPE = 16 |  | This error occur when SDK is about to process an unknown request type. Please contact our dev team to solve this issue.
UNKNOWN_VIEW_TYPE = 17 |  | This error occur when SDK is about to process an unknown view type. Please contact our dev team to solve this issue.
NO_IMAGE_SIZE_TYPE_INDEX = 18 | No matched view for ImageSizeIndex=%d | This error occur when SDK cannot show a loaded content because there is no view for the image size index of the loaded content.
INVALID_API_KEY = 102 | No app with the given api_key : | You may put wrong API Key in your code. Please check it again.
INVALIED_LOCALE = 102 | No locale match : l | Unknown locale is used for our service.


* * *

## Release Notes
- v2.3.1 _(11/27/2013 Updated)_ 
    - When GCM Registration ID is registred or changed, SDK now updates ID value to our service in real-time. (Previous SDKs only updated when app session started)
- v2.3.0 
    - Added a support for Baidu Push Notification. English version of Baidu push guide will be updated soon.
- v2.2.3
    - Added a support for title and ticker messages for Pus hNotification Campaign
    - Deprecated `AdFresca.generateNotification` method. Use `AdFresca.generateAFPushNotification()` instead
- v2.2.2 
    - Improved local cache features
- v2.2.1 
    -  Added 'Close mode' feature. You can control the closing action of an interstitial view on our dashboard.
- v2.2.0 
    - Added [Google Referrer Tracking](#google-referrer-tracking) feature.
    - Deprecated `AdFresca.setCustomParameter` method. Use  `setCustomParameterValue()` instead
    - Deprecated `AdFresca.setInAppPurchaseCount` method. Use `setNumberOfInAppPurchases()` instead
    - `setCustomParameterValue()` supports 64 bit integer (long type).
    -  Added local cache feature to cache custom parameters and in-app purchase count.
- v2.1.3 
    - Fix a minot bug that test mode was not able to test multiple Incentivized Campaign
- v2.1.2
    - Added `AFBannerView.setKeepAspectRatio(AFBannerView.KeepAspectRatio)`. It is able to set `keep_aspect_ratio` in java code.
- v2.1.1
    - Added `keep_aspect_ratio` attribute to `AFBannerView` to keep aspect ratio of the content for _Banner View_.
    - Deprecated all member variables of `AFRewardItem`. Please use getters of them instead.(They will be _private_ soon.)
- v2.1.0
    - Added `getAvailableRewardItems()`, `checkRewardItems()`, `checkRewardItems(boolean)`. You can give users reward items with _Incentivized Campaign_.
- v2.0.0
    - Deprecated `AdExceptionListner`, `AdException`. Please use `AFExceptionListner`, `AFException` instead.
- v2.0.0-beta.1
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
