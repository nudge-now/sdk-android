## Contents
- [Introduction](#introduction)
- [Quick Start](#quick-start)
    - [Installation](#installation)
    - [Code](#code)
- [Basic Features](#basic-features)
    - [In-App-Purchase Count](#in-app-purchase-count)
    - [Custom Parameter](#custom-parameter)
    - [Event Index](#event-index)
- [Custom Banner](#custom-banner)
    - [Floating View](#floating-view)
    - [Banner View](#banner-view)
- [Push Notification](#push-notification)
    - [Custom Notification](#custom-notification)
- [Advanced Features](#advanced-features)
    - [AFLoadListener](#afloadlistener)
    - [AFShowListener](#afshowlistener)
    - [Custom URI](#custom-uri)
    - [Test Device ID](#test-device-id)
    - [Timeout Interval](#timeout-interval)
- [Trouble Shooting](#trouble-shooting)
    - [Error Code](#error-code)
- [Release Notes](#release-notes)

* * *

## Introduction

_AD fresca_ basically display a campaign on a full screen. _Android SDK_ makes it possible to display a campaign by only several lines of code. In addition, A campaign is able to be displayed on diverse views and templates.

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

      <!-- Push Notification 기능을 사용할 경우, 아래 내용을 추가합니다. -->
      <activity android:name="com.adfresca.ads.AdFrescaPushActivity" />
        <receiver android:name="com.google.android.gcm.GCMBroadcastReceiver" android:permission="com.google.android.c2dm.permission.SEND">  
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="your_app_package" />
          </intent-filter>
      </receiver>
      <service android:name=".GCMIntentService" />  <!-- GCM 메시지를 처리하기 위하여 GCMIntentService 클래스를 구현해야 합니다. (9번 항목에서 상세 설명)  -->

   </application>

    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

    <!-- Push Notification 기능을 사용할 경우, 아래 내용을 추가합니다. -->
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

### Event Index

Using 'Event' feature, you can define your in-app events such as user's behavior, page navigation, and etc. Then you can target your campaigns for each event. 

For defining events on Dashboard, see ['Event Guide (Korean)'](https://adfresca.zendesk.com/entries/23359141)  

After you finished defining events, you need 'Event Index' value for each event. The index value is an integer value like '1,2,3,4'. We recommend to manage these values with Constant or Enum types in your source code.

To simply apply codes,  just pass event index into `AdFresca.load(int eventIndex)` method when the event occurs.

(If you don't specify event index on `AdFresca.load(int eventIndex)`, a default value is set to 1)

**Example**:  사용자가 메인 페이지로 이동할 시에 설정한 캠페인을 노출

```java
  public class MainPageActivity extends Activity {
    public void onCreate(Bundle savedInstanceState) {
      AdFresca adfresca = AdFresca.getInstance(this);     
      adfresca.load(EVENT_INDEX_MAIN_PAGE);  // 메인 페이지에 설정한  캠페인을 노출
      adfresca.show();
    }
  }
```

**Example**: 사용자의 게임 캐릭터가 레벨업을 했을 때 설정한 캠페인을 노출

```java
  public void **onUserLevelChanged**(int level) {
    AdFresca adfresca = AdFresca.getInstance(this);
    adfresca.setCustomParameter(CUSTOM_PARAM_INDEX_LEVEL, level); // 사용자 level 정보를 가장 최신으로 업데이트
    adfresca.load(EVENT_INDEX_LEVEL_UP);  // 레벨업 이벤트에 설정한 캠페인을 노출
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

_AD fresca_를 통해 Push Notification을 보내고 받을 수 있습니다.

SDK를 적용하기 이전에 구글의 ["GCM: Getting Started" ](http://developer.android.com/google/gcm/gs.html)가이드 문서를 읽어보시길 권장합니다.

1) GCM Helper Library 설치하기.
    - 구글에서 제공하는 [GCM Helper Library](http://code.google.com/p/gcm/source/browse/) 를 다운로드 받습니다. (Download zip 혹은 git clone을 이용)
    - /gcm-client/dist/**gcm.jar** 파일을 프로젝트에 복사하여 설치합니다.
    
2) AndroidManifest.xml 확인하기.

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
      <service android:name=".GCMIntentService" />  <!-- GCM 메시지를 처리하기 위하여 GCMIntentService 클래스를 구현해야 합니다 -->
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

3) Registration ID를 등록하고 SDK에 셋팅하기.

```java
    /*
    * GCM_SENDER_ID는 Google API project number를 의미 합니다.
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
  adfresca.setPushRegistrationId(gcmDeviceId);</strong> 
  adfresca.startSession();
```

4) GCMIntentService 클래스 구현하기

```java
  public class GCMIntentService extends GCMBaseIntentService {

    /*
    * GCM_SENDER_ID는 Google API project number를 의미 합니다.
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

      // AD fresca를 통해서 수신한 notification인지 확입합니다.
      if (AdFresca.isFrescaNotification(intent)) { 
        String title = context.getString(R.string.app_name);
        int icon = R.drawable.icon;
        long when = System.currentTimeMillis();

        // 수신 받은 notification을 status bar에 표시합니다.
        // notification에 URI Schema가 설정된 경우, 해당 URI를 실행하며 기본적으로는 targetClass에 설정한 액티비티를 실행하여 줍니다. 
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

_인트로 액티비티_에서 캠패인을 하고 끝나면 _메인 액티비티_로 이동

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
### Custom URI

Admin 사이트에서 Push Notification Campaign 생성 시, URI Schema를 입력받아 사용자가 notification 클릭 시 특정 액티비티로 바로 이동하도록 할 수 있습니다.

해당 기능을 지원하기 위해서는 AndroidManifest.xml 파일을 수정하여 URI 정보를 추가해야 합니다.

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

위와 같이 설정한 경우, Campaign의 URI Schema 값을 myapp://com.adfresca.zone 으로 설정하여 DemoZoneActivity가 바로 실행되도록 할 수 있습니다.

### Test Device ID

_AD fresca_는 테스트 모드 기능을 지원하며 테스트에 사용할 수 있는 기기를 별도로 등록하여 관리할 수 있습니다.

테스트 기기 ID는 SDK를 통해 추출이 가능하며 2가지 방법을 지원 합니다.


1. testDeviceId를 얻어와서 원하는 곳에 출력하는 방법

```java
AdFresca adfresca = AdFresca.getInstance(this);
String deviceId = adfresca.getTestDeviceId();
textView.setText(deviceId);
```

2. printTestDeviceId Property를 설정하여 광고 화면에 Device ID를 표시하는 방법
 
```java
  AdFresca adfresca = AdFresca.getInstance(this);
  Log.d(TAG, "AD fresca Test Device ID is = " + adfresca.getTestDeviceId());
  adfresca.setPrintTestDeviceId(true);
  adfresca.load();
  adfresca.show();
```

### Timeout Interval

광고의 최대 로딩 시간을 직접 지정하실 수 있습니다. 지정된 시간 내에 광고가 로딩되지 못한 경우, 사용자에게 광고룰 노출하지 않습니다.

최소 1초 이상 지정이 가능하며, 지정하지 않을 시 기본 값으로 5초가 지정 됩니다.

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
    - `AdFrescaView`가 deprecated 되었습니다. 새로 추가된 `AdFresca`를 사용해 주세요.
    - [Custom Banner](#custom-banner)([Floating View](#floating-view), [Banner View](#banner-view))가 추가되었습니다.
- v1.1.2
    - setIsInAppPurchasedCount(int) 메소드가  추가 되었습니다. In-App Purchase 구매 횟수를 관리 할 수 있습니다. (적용 방법은 5. In-App Purchase Count 관리 항목을 참고해주세요)
    - setIsInAppPurchasedUser(boolean) 메소드가 Deprecated 되었습니다. 새로 추가된 setIsInAppPurchasedCount(int) 메소드를 사용하여 주세요.
- v1.1.1
    - 광고 이미지 클릭 시 광고 뷰가 닫히도록 변경되었습니다.
    - 광고를 일정 시간 후 자동으로 닫을수 있는 Auto Close Timer 기능이 추가 되었습니다. Dashboard 에서 설정할 수 있습니다.
    - onAdClicked 이벤트가 Deprecated 되었습니다. onAdClicked 함수를 구현하지 않기를 권장합니다.
- v1.1.0
    - Event 기능을 지원합니다. loadAd() 메소드에 Event Index 값을 설정할 수 있습니다. 자세한 내용은 '7. Event 지정하기'를 참고해주세요.
    - AD Slot 기능이 Deprecated 되었습니다. 기존의 Default Slot은 '1'번 이벤트 인덱스,  AD Only Slot은 '2'번 이벤트 인덱스로 적용됩니다.
    - 광고 데이터를 요청하는 중에 새로 loadAd()가 호출된 경우, 가장 최근에 요청된 광고가 화면에 표시됩니다. (기존에는 광고 데이터를 요청 중에 새 요청을 할 수 없었습니다.)
    - AdListener에 onAdWillLoad, onAdClicked 이벤트가 추가 되었습니다. 광고 클릭 시 뷰를 다는 방법을 지원합니다. 자세한 내용은 '10. AdListener의 구현 및 다른 다양한 사용방법'을 참고해주세요.
- v1.0.1 
    - Push Notification 설정을 위하여 isFrescaNotification(), showNotification(), generateNotification() 메소드가 추가되었습니다. 자세한 내용은 'Push Notification 설정하기'를 참고해주세요
- v1.0.0 
    - 캐시 기능 및 퍼포먼스가 향상 되었습니다.
    - 서로 다른 액티비티 간에 loadAd(), showAd() 를 사용할 경우, 광고 이미지 클릭이 되지 않던 버그를 해결하였습니다.
    - setPushRegistrationId() 메소드가 추가되었습니다. 이후 업데이트될 푸시 서비스를 위해 사용자 GCM 아이디를 수집할 수 있습니다. (자세한 내용은 곧 업데이트 됩니다.)
- v0.9.9 
    - Custom Parameter를 지원합니다.  (자세한 내용은 'Custom Parameter 관리하기'를 참고해주세요)
- v0.9.8 
    - SDK 퍼포먼스가 향상 되었습니다.
    - 특정 조건에서 Request Timeout 문제 발생시 onAdClosed() 이벤트가 중복 호출되던 버그를 해결하였습니다.
- v0.9.7 
    - HTML5 형태의 View를 지원합니다. (SDK 적용 코드는 전혀 변경하지 않아도 됩니다.)
- v0.9.6
    - closeAd() 메소드가 추가 되었습니다.  사용자가  Back 버튼을 터치 시 광고뷰를 직접 닫을 수 있습니다. (자세한 내용은 'AdFrescaView 적용하기'를 참고해주세요)
    - 광고 로딩 시 특정 상황에서 Exception이 발생하던 문제를 해결하였습니다.
    - _AD fresca_ 로고가 왼쪽으로 정렬 됩니다. 
- v0.9.5 
    - 공지사항 기능이 추가 되면서 AD Slot 관리 기능이 추가 되었습니다. (자세한 내용은 'AD Slot 관리하기' 를 참고해 주세요)
    - 테스트 모드 기능 지원을 위한 테스트 기기 ID 확인 기능을 지원 합니다. (자세한 내용은 '테스트 기기 ID 확인하기'를 참고해 주세요)
    - 캐시 기능 및 퍼포먼스가 향상 되었습니다.
- v0.9.4
    - SDK가 광고 데이터를 캐싱하여 보여 줍니다. 광고를 1회 이상 노출 시 캐시가 자동으로 적용되어 빠른 노출이 가능하여 졌습니다.
    - AdFrescaView가 싱글톤 객체로 생성 됩니다. 각 액티비티 전환 시에도 동일한 뷰 객체를 사용 할 수 있으며 API Key는 최초 1회만 입력하면 됩니다.
    - timeoutInterval 설정 값이 추가 되었습니다. 지정된 시간 내에 광고를 로딩하지 못한 경우, 사용자에게 광고를 노출하지 않습니다. 최소 1초 이상 지정이 가능하며 기본 값은 기존의 5초로 설정 됩니다.
    - testModeEnabled 설정 값이 deprecated 되었습니다. 이후 모든 테스트 모드의 제어는 웹 Admin 페이지에서 가능합니다.
    - 안드로이드 4.1 버전을 지원합니다.
- v0.9.3
    - setIsInAppPurchasedUser(boolean) 메소드가  추가 되었습니다. In-App Purchase를 구매한 사용자들을 분류하여 관리 할 수 있습니다. (적용 방법은 5. In-App Purchased User 관리 항목을 참고해주세요)
- v0.9.2
    - 라이브러리 dependency 에러를 해결하였습니다.
    - Google Gson 및 OpenUDID를 별도로 사용하시는 경우, Duplicate 에러가 발생 할 수 있습니다. 추후 해당 이슈 해결이 포함된 버전을 릴리즈 할 예정입니다.
    - 0.9.1
    - 광고 호출 시 Timeout 처리 부분이 제대로 작동하지 않던 문제를 해결 하였습니다.
- v0.9.0
    - _AD fresca_ Android SDK가 출시 되었습니다. 기본적인 광고 호출 및 세션 로깅 기능을 지원 합니다.