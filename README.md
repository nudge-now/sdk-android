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
- [Reward Item](#reward-item)
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

_AD fresca_ 는 기본적으로 전면 이미지를 사용해서 캠페인을 합니다. Android SDK 는 단 몇줄의 코드만으로 이를 가능하게 합니다. 뿐만 아니라 다양한 형태의 캠페인 템플릿과 뷰를 통해 캠페인을 할 수 있습니다.

* * *

## Quick Start

### Installation

아래 링크를 통해 SDK 파일을 다운로드 합니다.

[Android SDK Download](http://file.adfresca.com/distribution/sdk-for-Android-beta.zip) (v2.0.0-beta.1)

**AdFresca.jar** 파일은 **lib** 폴더에, **adfresca_attr.xml** 파일은 **res/values** 폴더에 각각 복사합니다.

<img src="https://adfresca.zendesk.com/attachments/token/bja88u9zake4knm/?name=add_adfresca_jar_and_attr_xml.png" width="300"/>

프로젝트를 오른쪽 클릭 후, **Properties** 메뉴를 클릭합니다.

좌측 **Java Build Path** 메뉴에서 **Libraries** 탭을 들어간 후, **Add JARs** 버튼을 클릭하여 **AdFresca.jar** 파일을 추가합니다.

<img src="https://adfresca.zendesk.com/attachments/token/ogcnzf3kmyzbcvg/?name=add_jar.png" width="600" />

**AndroidManifest.xml**의 Permission 추가하기

_AD fresca_ 는 사용자의 네트워크 접속 상태, 기기ID를 수집하여, 컨텐츠 매칭에 사용합니다. 이를 위해 관련 퍼미션을 등록 및 허용해 주어야 합니다. 수집되는 모든 데이터는 암호화 처리되어 전송되며 컨텐츠 매칭 이외의 목적에 사용되지 않습니다 아래와 같이 Permission을 추가합니다.

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

_AD fresca SDK_는 아래 코드 처럼 단 몇줄의 코드만으로도 캠페인을 시작할 수 있습니다.

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

`AdFresca.setApiKey(API_KEY);` API Key 를 설정합니다.

`adfresca.startSession();` 세션이 시작됨을 서버에 알립니다. 어플리케이션이 시작될 때 **한번만** 실행되도록 합니다.

`adfresca.load();` 서버로부터 캠페인을 내려받습니다.

`adfresca.show();` 내려받은 캠페인을 보여줍니다.

정상적으로 실행이 되면 다음과 같은 화면이 보여집니다.

<img src="https://adfresca.zendesk.com/attachments/token/zngvftbmcccyajk/?name=device-2013-03-18-133517.png" width="240" />
&nbsp;
<img src="https://adfresca.zendesk.com/attachments/token/phn4fcpvbi2damx/?name=device-2013-03-18-133443.png" height="240" />
* * *

## Basic Features

### In-App Purchase Count

사용자가 In-App Purchase를 구매한 경우, _AD fresca_에 해당 정보를 기록하여 관리하실 수 있습니다.

사용자가 In-App Purchase 를 구매한 횟수를  AdFresca 객체의 setInAppPurchaseCount(int) 메소드를 사용하여  설정해 주시면 간단히 적용이 가능합니다.

```java
  AdFresca adfresca = AdFresca.getInstance(this);
  AdFresca.setIsInAppPurchasedUser(User.getInAppPurchaseCount());
  adfresca.startSession();
  adfresca.load();
  adfresca.show();
```

위와 같은 방식으로 호출이 가능합니다.

정확한 기록을 위해 반드시 앱이 실행되었다고 판단되는 시점에서 호출하는 것을 권장합니다.

추후 보다 편하게 해당 기능을 이용하실 수 있도록 지원해드릴 예정입니다.

### Custom Parameter

_AD fresca_는 앱 사용자의 특수한 정보들 (레벨, 스테이지, 성별, 나이 등)을 입력 받아 타겟팅 및 분석 기능을 제공 합니다.

SDK에서는 **setCustomParameter** 메소드를 사용하여 각 커스텀 파라미터의 인덱스 번호에 맞게 값을 설정하면 됩니다.

(각 파라미터의 정보는 Admin 사이트를 접속하여 앱의 Overview 메뉴 -> 각 앱스토어의 Details 버튼을 눌러 설정 및 확인이 가능합니다.)

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

Event 기능을 사용하면 앱에서 일어나는 다양한 사용자들의 활동, 페이지 이동 등에 Event를 설정한 후 그러한 Event 발생 시에 그에 적합한 공지사항, 컨텐츠 노출 등의 캠페인을 노출할 수 있습니다.

Event 설정은 Admin 을 통해 가능하며 '[Event 설정하기](https://adfresca.zendesk.com/entries/23359141)' 가이드를 참고해주시기 바랍니다.

Event 설정하신 후, SDK 적용을 위해서는 각 Event 'Index' 값이 필요합니다. Index 값은 1,2,3,4 와 같은 Integer 형태의 고유 값이며 소스코드에 Constant 형태로 미리 입력하여 이용하시는 것을 권장합니다.

각 Event 발생 시, load() 메소드에 원하는 Event의  Index 값을 인자로 넘겨주시면 간단히 적용이 완료됩니다.

_(기존의 ['AD Slot 지정하기](https://adfresca.zendesk.com/entries/23359131)' 기능은 Deprecated 되어 현재 Event로 대체 되었습니다. 자세한 내용은 SDK Changed Log를 확인하여 주세요. )_


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
  public void onUserLevelChanged(int level) {
    AdFresca adfresca = AdFresca.getInstance(this);
    adfresca.setCustomParameter(CUSTOM_PARAM_INDEX_LEVEL, level); // 사용자 level 정보를 가장 최신으로 업데이트
    adfresca.load(EVENT_INDEX_LEVEL_UP);  // 레벨업 이벤트에 설정한 캠페인을 노출
    adfresca.show();
  }
```

* * *

## Custom Banner

Android SDK 에서는 _Floating View_와 _Banner View_ 두가지 종류의 커스텀 배너를 제공합니다. 커스텀 배너는 dashboard 에서 이미지 사이즈를 등록한 후 해당 이미지 사이즈를 사용하는 캠페인이 매칭되었을 때 이미지를 커스텀 배너에 보여줍니다.

`AdFresca.load()` 와 `AdFresca.show()` 를 통해 이미지를 보여주는 점은 기존 캠페인과 같습니다. _Floating View_는 다른 UI Component 위에 위치하며 닫을 수 있으며, _Banner View_는 _Floating View_와는 반대로 화면의 일정 영역을 차지하며 닫을 수 없습니다. 

커스텀 배너를 사용하기 위해서는 아래와 같이 namespace 를 layout xml 파일에 추가해야합니다.

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:adfresca="http://schemas.android.com/apk/res/Your.Package.Name"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
</LinearLayout>
```

### Floating View

Floating View 를 사용하기 위해 태그를 추가합니다.

```xml
<com.adfresca.sdk.view.AFFloatingView
    android:layout_width="match_parent"
    android:layout_height="80dp"
    adfresca:image_size_index="1" />
```

*   `adfresca:image_size_index=1` _이미지 사이즈 인덱스_를 설정합니다.

닫기 버튼 이미지를 설정하여 사용자가 _Floating View_를 닫을 수 있도록 할 수 있습니다.

```xml
<com.adfresca.sdk.view.AFFloatingView
    android:layout_width="match_parent"
    android:layout_height="80dp"
    adfresca:image_size_index="1"
    adfresca:close_button_image="@drawable/close_button" />
```

*   `adfresca:close_button_image="@drawable/close_button"` 닫기 버튼 이미지를 설정합니다.

### Banner View

_Banner View_ 를 사용하기 위해 태그를 추가합니다.

```xml
<com.adfresca.sdk.view.AFBannerView
    android:layout_width="match_parent"
    android:layout_height="80dp"
    adfresca:default_image="@drawable/ic_launcher"
    adfresca:image_size_index="1"/>
```

*   `adfresca:image_size_index="1"` 이미지 사이즈 인덱스를 설정합니다.
*   `adfresca:default_image="@drawable/default_image"` 이미지가 로드되기 전 표시할 디폴트 이미지를 지정합니다.

**Example:** 한 액티비티에서 기본 _Interstitial View_와 _Banner View_ 두 개의 View를 동시에 사용하기

이벤트 기능을 활용하여 여러 개의 뷰를 한 화면에 동시에 노출할 수 있습니다. 

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);
  setContentView(R.layout.activity_intro);
  
  AdFresca.setApiKey(API_KEY);
  AdFresca adfresca = AdFresca.getInstance(this);
  adfresca.startSession();
  adfresca.load(EVENT_INDEX_MAIN_PAGE_FOR_BANNER); // 메인 페이지진입 시 Banner View 를 위한 컨텐츠를 load 합니다.
  adfresca.load(EVENT_INDEX_MAIN_PAGE_FOR_INTERSTITIAL); // 메인 페이지 진입 시 Interstitial View 를 위한 컨텐츠를 load 합니다.
  adfresca.show(); // load 된 모든 컨텐츠를 show 합니다.
}
```

* * *

## Push Notification

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
  adfresca.setPushRegistrationId(gcmDeviceId);
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

아래 코드는 Push Notification 을 만들고 직접 notify 하는 방법입니다.

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

## Reward Item

Incentivized Campaign을 사용하여 , 사용자가 Media App에서 Advertising App의 광고를 보고 앱을 설치하였을 때 보상으로 Media App의 아이템을 지급할 수 있습니다.

(현재 CPI  캠페인을 진행할 경우, Advertising App의 SDK 설치는 필수가 아닙니다. 하지만 이후 지원할 CPA 캠페인을 위해서 미리 SDK를 설치하는 것을 권장합니다.)

Media App에 SDK 적용하기:

- 아이템 지급을 원하는 위치에서 getAvailableRewardItems() 메소드를 통해 아이템 리스트를 받습니다. 

- Array에는 AFRewardItem 객체들이 포함되어 있으며 name, quantity, uniqueValue 프로퍼티 값을 가지고 있습니다.

- getAvailableRewardItems() 메소드는 현재 지급이 가능한 아이템 리스트를 리턴하며, 아직 검사가 끝나지 않은 경우 이후 메소드 호출 시에 반영됩니다.

```java
@Override
public void onStart() {
  super.onStart();

  AdFresca adfresca = AdFresca.getInstance(DemoIntroActivity.this);
  List<AFRewardItem> items = adfresca.getAvailableRewardItems();

  if (items.size() > 0) {
    for (AFRewardItem item : items) {
      Log.d(TAG,String.format("Get AFRewardItem: item.name=%s, item.quantity=%d, item.uniqueVlaue=%s", item.name, item.quantity, item.uniqueValue));
      // do something with item.name, item.uniqueValue and item.quantity
    }
    String itemNames = joinNameStringsByComma(items);
    String alertMessage = String.format("You got the reward item(s)! (%s)", itemNames);
    showAlert(alertMessage);
  }
}
```

###(Advanced) 더욱 빠르게 아이템 지급하기:

getAvailableRewardItems() 메소드는 현재 지급 가능한 아이템 리스트를 리턴한 이후, 새롭게 지급 가능한 아이템들이 있는지 백그라운드로 검사를 진행하게 됩니다. 만약 앱 시작시에 미리 검사를 수동으로 진행하고 원하는 위치에서 getAvailableRewardItems() 메소드를 호출한다면, 사용자들에게 더욱 빠른 아이템 지급이 가능해집니다.

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
      Log.d(TAG,String.format("Get AFRewardItem: item.name=%s, item.quantity=%d, item.uniqueVlaue=%s", item.name, item.quantity, item.uniqueValue));
      // do something with item.name, item.uniqueValue and item.quantity
    }
    String itemNames = joinNameStringsByComma(items);
    String alertMessage = String.format("You got the reward item(s)! (%s)", itemNames);
    showAlert(alertMessage);
  }
}
```

checkRewardItems(synchronized) 메소드를 Synchronized 모드로 실행하면,, SDK가 모든 검사를 완료할 때 까지 기다린 후 바로 아이템을 지급할 수도 있습니다.

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
        Log.d(TAG,String.format("Get AFRewardItem: item.name=%s, item.quantity=%d, item.uniqueVlaue=%s", item.name, item.quantity, item.uniqueValue));
        // do something with item.name, item.uniqueValue and item.quantity
      }
      String itemNames = joinNameStringsByComma(items);
      String alertMessage = String.format("You got the reward item(s)! (%s)", itemNames);
      showAlert(alertMessage);
    }
  }
}.execute();
```

* * *

## Advanced Features

### AFLoadListener

AFLoadListener는 [Custom Parameter](#custom-parameter), [In-App-Purchase](#in-app-purchase) 등을 편리하게 업데이트 할 수 있도록 합니다. `AFLoadListener.onStart()`는 `AdFresca.load()`가 호출될 때 마다 호출되기 때문에 다음과 같이 `AFLoadListener`를 구현하고 설정하면 언제든지 최신 값으로 캠페인을 로드할 수 있습니다.

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

`AFShowListener`는 캠패인이 끝났을 때 처리를 위한 _이벤트 리스너_입니다.

캠패인이 끝났다는 것은 다음 두가지를 의미합니다.

1. 컨텐츠가 정상적으로 화면에 보여지고 닫혀진 경우
2. load 된 컨텐츠가 만료되었거나 컨텐츠에 맞는 view를 찾을 수 없어서 보여지지 않고 끝난 경우

이 두가지 경우를 `AFShowListener.show(int eventIndex, AFView view)`의 두번째 인자 `view`로 판별할 수 있습니다.

- `view != null`이면 컨텐츠가 정상적으로 보여진 경우입니다. 이때 `view`는 [ _Default View_ | _Floating View_ | _Banner View_ ] 가 됩니다. `AFView.isDefaultView()`로 _Default View_ 인지 판별할 수 있습니다.
- `view == null`이면 컨텐츠가 보여지지 않고 끝난 경우입니다.

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
**Example:** _인트로 액티비티_에서 컨텐츠를 보여주고 끝나면 _메인 액티비티_로 이동

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

**_주의!_**
사용자가 마켓이나 다른 애플리케이션의 URI가 설정된 컨텐츠를 클릭한 경우, 화면이 다른 애플리케이션으로 이동할 수 있습니다.

이 때 onAdClosed 에 다른 Activity 로 이동하도록 구현하였다면, 사용자가 다른 화면에 있는 동안 앱의 Activity 가 미리 이동해버릴 수 있습니다.

아래와 같은 방법으로 해당 문제를 해결할 수 있습니다.

Dashboard 에서 Event 의 Close Mode 를 Override 로 변경 합니다.(컨텐츠를 클릭해도 컨텐츠가 닫히지 않는다.)
onResume() 을 다음과 같이 구현합니다.

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

Announcement 캠페인의 Click URL, Push Notification 캠페인의 URL Schema 설정 시에 자신의 앱 URL Schema를 사용할 수 있습니다.

이를 통해 사용자가 콘텐츠를 클릭할 경우, 자신이 원하는 특정 앱 페이지로 이동하는 등의 액션을 지정할 수 있습니다.

해당 기능을 지원하기 위해서는 AndroidManifest.xml 파일을 수정하여 scheme 정보를 추가해야 합니다.

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

위와 같이 설정한 경우, Campaign의 _Click URL_ 값을 myapp://com.adfresca.zone 으로 설정하여 DemoZoneActivity가 바로 실행되도록 할 수 있습니다.

### Test Device ID

_AD fresca_는 테스트 모드 기능을 지원하며 테스트에 사용할 수 있는 기기를 별도로 등록하여 관리할 수 있습니다.

테스트 기기 ID는 SDK를 통해 추출이 가능하며 2가지 방법을 지원 합니다.


1. testDeviceId를 얻어와서 원하는 곳에 출력하는 방법

```java
AdFresca adfresca = AdFresca.getInstance(this);
String deviceId = adfresca.getTestDeviceId();
textView.setText(deviceId);
```

2. printTestDeviceId Property를 설정하여 화면에 Device ID를 표시하는 방법
 
```java
  AdFresca adfresca = AdFresca.getInstance(this);
  Log.d(TAG, "AD fresca Test Device ID is = " + adfresca.getTestDeviceId());
  adfresca.setPrintTestDeviceId(true);
  adfresca.load();
  adfresca.show();
```

### Timeout Interval

컨텐츠의 최대 로딩 시간을 직접 지정하실 수 있습니다. 지정된 시간 내에 컨텐츠가 로딩되지 못한 경우, 사용자에게 컨텐츠를 노출하지 않습니다.

최소 1초 이상 지정이 가능하며, 지정하지 않을 시 기본 값으로 5초가 지정 됩니다.

```java
  AdFresca adfresca = AdFresca.getInstance(this);
  AdFresca.setTimeoutInterval(5) // # 5 seconds
  adfresca.load();
  adfresca.show();
```

* * *

## Trouble Shooting

컨텐츠 제대로 출력되지 않거나, 에러가 발생한다면 AdExceptionListener 인터페이스를 구현하여, 에러 정보를 확인 할 수 있습니다.

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
UNKNOWN_ERROR = 1 | Unknown Error | 아직 처리되지 않은 에러를 뜻합니다. 자세한 내용을 AdFresca 개발팀에게 문의해주시면 대응이 가능합니다.
NO_APIKEY = 1 | No API Key set to AdFresca | AdFresca 에 API Key 를 설정하지 않은 경우 발생합니다.
NO_ACTIVITY = 2 | No Activity set to AdFresca | AdFresca.getInstance(Activity) 에 Activity 가 넘어오지 않은 경우 발생합니다.
NO_INTERNET_CONNECTION = 3 | No Internet Connection | 디바이스의 인터넷 접속이 불가능할 시에 발생합니다. android.permission.INTERNET  퍼미션이 설정되지 않을 시에 발생 할 수 있습니다.
NO_DEVICE_ID = 4 |  | OpenUDID 가 제대로 등록되지 않은 경우 발생할 수 있습니다.
NO_NETWORK_STATUS = 5 |  | ACCESS_NETWORK_STATE 퍼미션이 설정되지 않은 경우 발생합니다.
INVALID_ADREQEST_IMPRESSION = 6 | We're sorry, but something went wrong. /impression | 서버에서 응답을 제대로 처리하지 못하는 경우 발생 합니다. AdFresca 개발팀에게 직접 문의해주시면 대응이 가능합니다.
INVALID_ADREQEST_IMAGE = 7 | We're sorry, but something went wrong. /image | 서버에서 응답을 제대로 처리하지 못하는 경우 발생 합니다. AdFresca 개발팀에게 직접 문의해주시면 대응이 가능합니다.
INVALID_ADREQEST_DISPLAYED = 8 | We're sorry, but something went wrong. /displayed | 서버에서 응답을 제대로 처리하지 못하는 경우 발생 합니다. AdFresca 개발팀에게 직접 문의해주시면 대응이 가능합니다.
INVALID_ADREQEST_CLICKED = 9 | We're sorry, but something went wrong. /clicked | 서버에서 응답을 제대로 처리하지 못하는 경우 발생 합니다. AdFresca 개발팀에게 직접 문의해주시면 대응이 가능합니다.
AD_TIMEOUT = 10 | Request Timed Out | 컨텐츠 로딩이 지정된 시간을 초과하여 켐패인 노출이 취소된 경우 발생합니다. 
NO_APP_VERSION = 11 | No app version in manifest.xml | manifest.xml 안에 versionName이 명시되지 않은 경우 발생합니다.
INVALID_SESSION_REQUEST = 12 | We're sorry, but something went wrong. /session | 서버에서 응답을 제대로 처리하지 못하는 경우 발생 합니다. AdFresca 개발팀에게 직접 문의해주시면 대응이 가능합니다.
INVALID_ADREQEST_ACTIVE = 13 | We're sorry, but something went wrong. /active | 서버에서 응답을 제대로 처리하지 못하는 경우 발생 합니다. AdFresca 개발팀에게 직접 문의해주시면 대응이 가능합니다.
AD_IMPRESSION_EXPIRED = 14 | This AD is expired | 이전에 받아온 컨텐츠가 더이상 유효하지 않을 경우 발생 합니다.
INVALID_PUSH_RUN_REQUEST = 15 | We're sorry, but something went wrong. /push_notification | 서버에서 응답을 제대로 처리하지 못하는 경우 발생 합니다. AdFresca 개발팀에게 직접 문의해주시면 대응이 가능합니다.
UNKNOWN_REQUEST_TYPE = 16 |  | SDK 에 알 수 없는 요청이 들어온 경우 발생합니다. AdFresca 개발팀에게 직접 문의해주시면 대응이 가능합니다.
UNKNOWN_VIEW_TYPE = 17 |  | SDK 에 알 수 없는 View Type 이 처리되는 경우 발생합니다. AdFresca 개발팀에게 직접 문의해주시면 대응이 가능합니다.
NO_IMAGE_SIZE_TYPE_INDEX = 18 | No matched view for ImageSizeIndex=%d | 서버로부터 내려받은 컨텐츠의 Image Size Index 와 매칭되는 View 가 없어서 노출하지 못한 경우에 발생합니다.
INVALID_API_KEY = 102 |  No app with the given api_key : | 유효하지 않은 API Key를 입력한 경우 발생합니다. 발급된 API Key가 맞는지 다시 한번 확인해 주세요.
INVALIED_LOCALE = 102 | No locale match : l | 디바이스에서 아직 제공하지 않는 언어(로케일)을 사용시 발생합니다.

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
