## Contents
- [Introduction](#introduction)
- [Quick Start](#quick-start)
    - [Installation](#installation)
    - [Code](#code)
- [Test Device ID](#test-device-id)
- [Custom Parameter](#custom-parameter)
- [Marketing Event](#marketing-event)
- [In-App-Purchase Count](#in-app-purchase-count)
- [Push Notification](#push-notification)
    - [Custom Notification](#custom-notification)
    - [Image Notification](#image-notification)
    - [Baidu Push Service](#baidu-push-service)
- [Custom URL](#custom-url)
- [In-App Purchase Tracking (Beta)](#in-app-purchase-tracking-beta)
- [CPI Identifier](#cpi-identifier)
- [Reward Item](#reward-item)
- [Custom Banner (Android Only)](#custom-banner)
    - [Floating View](#floating-view)
    - [Banner View](#banner-view)
- [Advanced Features](#advanced-features)
    - [AFLoadListener](#afloadlistener)
    - [AFShowListener](#afshowlistener)
    - [Timeout Interval](#timeout-interval)
    - [Google Referrer Tracking](#google-referrer-tracking)
- [Proguard Configuration](#proguard-configuration)
- [Trouble Shooting](#trouble-shooting)
- [Release Notes](#release-notes)

* * *

## Introduction

AD fresca는 게임 운영자나 마케터가 앱 내 사용자 특성을 실시간으로 파악하여  더 자주, 더 오래 플레이하고, 더 많이 결제하도록 유도하는 라이브 서비스 운영 툴을 제공합니다

게임 운영자나 마케터는 [Dashboard](https://admin.adfresca.com) 사이트를 통해 실시간으로 타겟팅한 사용자에게 메시지를 전달할 수 있으며, 이를 실제 앱에 적용하기 위하여 게임 개발팀에서는 아래 제공되는 SDK를 손쉽게 설치하고 가이드에 따라 코드를 적용합니다.

* * *

## Quick Start

### Installation

아래 링크를 통해 SDK 파일을 다운로드 합니다.

[Android SDK Download](http://file.adfresca.com/distribution/sdk-for-Android.zip) (v2.3.4)

[Android SDK Download without Gson Library](http://file.adfresca.com/distribution/sdk-for-Android-wihtout-gson.zip) (v2.3.4)

[Android SDK with IAP Tracking Beta Download](http://file.adfresca.com/distribution/sdk-for-Android-iap-beta.zip) (v.2.4.0-beta4)

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

      <!-- Incentivized Campaign 을 위한 액티비티-->
      <activity android:name="com.adfresca.sdk.reward.AFRewardActivity" />
      
       <!-- Google Referrer Tracking 을 위한 Boradcast Receiver-->
      <receiver android:name="com.adfresca.sdk.referer.AFRefererReciever" android:exported="true">
      	<intent-filter>
            <action android:name="com.android.vending.INSTALL_REFERRER" />
     	</intent-filter>	
      </receiver>
            
      <!-- Push Notification 기능을 사용하기 위하여 아래 내용을 추가합니다. -->
      <activity android:name="com.adfresca.ads.AdFrescaPushActivity" />
      <receiver android:name="com.google.android.gcm.GCMBroadcastReceiver" android:permission="com.google.android.c2dm.permission.SEND">  
        <intent-filter>
          <action android:name="com.google.android.c2dm.intent.RECEIVE" />
          <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
          <category android:name="your_app_package" />
        </intent-filter>
      </receiver>
      <service android:name=".GCMIntentService" />  <!-- GCM 메시지를 처리하기 위하여 GCMIntentService 클래스를 구현해야 합니다. Push Notification 항목 참고)  -->

   </application>

    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

    <!-- Push Notification 기능을 사용하기 위하여 아래 내용을 추가합니다. -->
    <permission android:name="your_app_pakcage.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="your_app_package.permission.C2D_MESSAGE" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
    <uses-permission android:name="android.permission.READ_PHONE_STATE" /> 
    <uses-permission android:name="android.permission.VIBRATE" />

</manifest>
```

### Code

AD fresca SDK 통해 사용자에게 메시지를 전달하기 위한 주요 코드를 적용합니다. 아래의 코드만으로도 게임 운영자 / 마케터가 지정한 캠페인의 콘텐츠를 화면에 표시할 수 있습니다.

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

`AdFresca.setApiKey(API_KEY);` API Key 를 설정합니다. API Key는 [Dashboard](https://admin.adfresca.com) 사이트에서 앱 추가 후 Overview 메뉴의 Settings - API Keys 버튼을 클릭하여 확인이 가능합니다.

`adfresca.startSession();` 앱이 실행되었음을 기록합니다. 어플리케이션이 시작될 때 **한번만** 실행되도록 합니다.

`adfresca.load();` 서버로부터 매칭되는 캠페인의 콘텐츠를 내려받습니다. 

`adfresca.show();` 내려받은 콘텐츠를 화면에 표시합니다.

앱이 실행 되면 다음과 같은 화면이 보여집니다. 정상적으로 콘텐츠 뷰가 화면에 표시되고, 터치 시 앱스토어 페이지로 이동하는지 확인합니다.

<img src="https://adfresca.zendesk.com/attachments/token/zngvftbmcccyajk/?name=device-2013-03-18-133517.png" width="240" />
&nbsp;
<img src="https://adfresca.zendesk.com/attachments/token/phn4fcpvbi2damx/?name=device-2013-03-18-133443.png" height="240" />
* * *

### Test Device ID

AD fresca는 테스트 모드 기능을 지원하여 테스트를 원하는 디바이스에만 지정한 캠페인의 콘텐츠를 화면에 표시하고 푸시 메시지를 전송할 수 있습니다. 이로 인해 SDK가 적용된 앱이 이미 앱스토어에 출시된 경우, 게임 운영팀 혹은 개발팀에게만 새로운 메시지를 전달할 수 있도록 지원합니다.

테스트 기기 등록을 위한 아이디 값은 SDK를 통해 추출이 가능하며 2가지 방법을 지원 합니다.
 
1. getTestDeviceId() 메소드를 사용하여 로그로 출력하는 방법
  - 테스트에 사용할 기기를 개발PC에 연결한 후 로그를 통해 해당 아이디 값을 출력하여 확인 합니다. 

  ```java
  AdFresca adfresca = AdFresca.getInstance(this);
  Log.d(TAG, "AD fresca Test Device ID is = " + adfresca.getTestDeviceId());
  ```

2. setPrintTestDeviceId() 메소드를 사용하여 콘텐츠 뷰에 기기 아이디를 화면에 표시하는 방법
  - 개발자가 기기를 직접 연결할 수 없는 경우, 설정을 활성화 한 상태로 앱 빌드를 전덜하여 설치합니다. 화면에 표시된 기기 아이디를 직접 기록하여 등록할 수 있습니다.
  - 담당 마케터가 원격에서 근무하는 경우 해당 기능을 유용하게 사용할 수 있습니다.
  - 설정이 활성화된 상태로 앱이 배포되지 않도록 주의해야 합니다.

  ```java
    AdFresca adfresca = AdFresca.getInstance(this);
    adfresca.setPrintTestDeviceId(true);
    adfresca.load();
    adfresca.show();
  ```

* * *

### Custom Parameter

커스텀 파라미터는 캠페인 진행 시, 타겟팅을 위해 사용할 사용자의 상태 값을 의미합니다.

AD fresca SDK는 기본적으로 '국가, 언어, 앱 버전, 실행 횟수 등'의 디바이스 고유 데이터를 수집하며, 동시에 각 앱 내에서 고유하게 사용되는 특수한 상태 값들(예: 캐릭터 레벨, 보유 포인트, 스테이지 등)을 커스텀 파라미터로 정의하고 수집하여 분석 및 타겟팅 기능을 제공합니다.

커스텀 파라미터 설정은 [Dashboard](https://admin.adfresca.com) 사이트를 접속하여 앱의 Overview 메뉴 -> Settings - Custom Parameters 버튼을 클릭하여 확인할 수 있습니다.

SDK 적용을 위해서는 Dashboard에서 지정된 각 커스텀 파라미터의 '인덱스' 값이 필요합니다. 인덱스 값은 1,2,3,4 와 같은 Integer 형태의 고유 값이며 소스코드에 Constant 형태로 지정하여 이용하는 것을 권장합니다.

Integer, Boolean 형태의 데이터를 상태 값으로 설정할 수 있으며, *setCustomParameterValue** 메소드를 사용하여 각 인덱스 값에 맞게 상태 값을 설정합니다.

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

**주의**_ setCustomParameterValue() 메소드는 startSession(), load() 메소드 이전에 호출이 되어야 합니다. 특히 startSession() 이전에는 반드시 모든 커스텀 파리미터 값들을 설정하고, 이후 변경되는 값들에 한하여 각 위치에 커스텀 파라미터를 설정합니다.

만약 불가피하게 startSession() 호출 전에 커스텀 파라미터 값을 설정할 수 없는 경우, 앱을 최초로 실행한 사용자의 프로파일은 업데이트되지 않으며 해당 사용자의 2회째 앱 실행부터 SDK가 로컬에 캐싱해둔 값이 전달됩니다. 최초로 실행된 사용자의 프로파일까지 통계 및 타겟팅하기 위해서는 아래와 같이 초기 값 설정을 진행합니다. 또한, 사용자의 로그인 이벤트 이후 모든 커스텀 파라미터의 값을 설정할 수 있도록 구현합니다.

```cs
  public void onCreate() {
    AdFresca adfresca = AdFresca.getInstance(this);     
    if (isFirstRun) {
      adfresca.setCustomParameterValue(CUSTOM_PARAM_INDEX_LEVEL, defaultLevel);
      adfresca.setCustomParameterValue(CUSTOM_PARAM_INDEX_STAGE, defaultStage);
      adfresca.setCustomParameterValue(CUSTOM_PARAM_INDEX_HAS_FB_ACCOUNT, defaultFacebookFlag);
    }    
    adfresca.startSession();
  }
  
  .....
  
  public void onUserSignedIn() {
    AdFresca adfresca = AdFresca.getInstance(this);     
    adfresca.setCustomParameterValue(CUSTOM_PARAM_INDEX_LEVEL, User.level);
    adfresca.setCustomParameterValue(CUSTOM_PARAM_INDEX_AGE, User.age);
    adfresca.setCustomParameterValue(CUSTOM_PARAM_INDEX_HAS_FB_ACCOUNT, User.hasFacebookAccount);
  }
```

(Advanced) SDK는 현재 설정한 Custom Parameter 값을 로컬에 저장하여 두고 있습니다. 특정 이슈가 발생하여 해당 값을 확인 및 초기화 시키고 싶은 경우 getCustomParameterValue(index), resetCustomParameterValues() 메소드를 사용할 수 있습니다.

* * *

## Marketing Event

마케팅 이벤트는 유저에게 메세지를 전달하고자 하는 상황을 의미합니다. (예: 캐릭터 레벨 업, 퀘스트 달성, 스토어 페이지 진입)

마케팅 이벤트 기능을 사용하여 지정된 상황에 알맞는 캠페인이 노출되도록 할 수 있습니다.

마케팅 이벤트 설정은 [Dashboard](https://admin.adfresca.com) 사이트를 접속하여 앱의 Overview 메뉴 -> Settings - Marketing Events 버튼을 클릭하여 확인할 수 있습니다.

SDK 적용을 위해서는 Dashboard에서 지정된 각 마케팅 이벤트의 '인덱스' 값이 필요합니다. 인덱스 값은 1,2,3,4 와 같은 Integer 형태의 고유 값이며 소스코드에 Constant 형태로 지정하여 이용하는 것을 권장합니다.

각 이벤트 발생 시, load() 메소드에 원하는 이벤트 인덱스 값을 인자로 넘겨주시면 간단히 적용이 완료됩니다.

(load() 메소드에 인덱스를 설정하지 않은 경우, 인덱스 값은 '1' 값이 자동으로 지정됩니다.)

**Example**:  사용자가 메인 페이지로 이동할 시에 설정한 컨텐츠를 노출

```java
  public class MainPageActivity extends Activity {
    public void onCreate(Bundle savedInstanceState) {
      AdFresca adfresca = AdFresca.getInstance(this);     
      adfresca.load(EVENT_INDEX_MAIN_PAGE);  // 메인 페이지에 설정한  컨텐츠를 노출
      adfresca.show(EVENT_INDEX_MAIN_PAGE);
    }
  }
```

**Example**: 사용자의 게임 캐릭터가 레벨업을 했을 때 설정한 컨텐츠를 노출

```java
  public void onUserLevelChanged(int level) {
    AdFresca adfresca = AdFresca.getInstance(this);
    adfresca.setCustomParameterValue(CUSTOM_PARAM_INDEX_LEVEL, level); // 사용자 level 정보를 가장 최신으로 업데이트
    adfresca.load(EVENT_INDEX_LEVEL_UP);  // 레벨업 이벤트에 설정한 컨텐츠를 노출
    adfresca.show(EVENT_INDEX_LEVEL_UP);
  }
```

* * *

## In-App Purchase Count

앱에서 IAP 기능을 사용하는 경우, 현재까지 사용자가 구매한 누적 횟수를 SDK에 설정하여 분석 및 타겟팅에 이용할 수 있습니다. 

**setNumberOfInAppPurchases(int)** 메소드를 사용하여 현재까지 사용자가 구매한 누적 횟수 값을 SDK에 설정합니다. 커스텀 파라미터와 마찬가지로 앱 실행 혹은 사용자 로그인 이후에 값을 지정하고, IAP 결제가 일어난 직후에 갱신된 누적 구매 횟수 값을 설정합니다.

```java
  AdFresca adfresca = AdFresca.getInstance(this);
  
  public void onCreate() {
    AdFresca adfresca = AdFresca.getInstance(this);     
    adfresca.setNumberOfInAppPurchases(User.inAppPurchaseCount);
    adfresca.startSession();
  }
  
  .....
  
  public void onUserPurchasedItem(int totalPurchaseCount) {
    User.inAppPurchaseCount = totalPurchaseCount;
    
    AdFresca adfresca = AdFresca.getInstance(this);     
    adfresca.setNumberOfInAppPurchases(User.inAppPurchaseCount);
  }
```

(Advanced) SDK는 현재 설정한 inAppPurchaseCount 값을 로컬에 저장하여 두고 있습니다. 특정 이슈가 발생하여 해당 값을 확인 및 초기화 시키고 싶은 경우 getNumberOfInAppPurchases(), resetNumberOfInAppPurchases() 메소드를 사용할 수 있습니다.

* * *

## Push Notification

_AD fresca_를 통해 Push Notification을 보내고 받을 수 있습니다.

SDK를 적용하기 이전에 [Google API Console](https://cloud.google.com/console) 사이트에서 프로젝트를 생성하고, [Dashboard](https://admin.adfresca.com) 사이트에 설정할 GCM API Key 및 SDK 적용에 필요한 GCM_SENDER_ID (Project Number) 값을 얻어야 합니다.

'[Android Push Notification 설정 및 적용하기 (GCM)](https://adfresca.zendesk.com/entries/28526764)' 가이드를 참고하여 필요한 값들을 얻습니다.

이제 SDK 적용을 시작합니다.

1) GCM Helper Library 설치하기
    - 구글에서 제공하는 [GCM Helper Library](http://code.google.com/p/gcm/source/browse/) 를 다운로드 받습니다. (Download zip 혹은 git clone을 이용)
    - /gcm-client/dist/**gcm.jar** 파일을 프로젝트에 복사하여 설치합니다.
    
2) AndroidManifest.xml 확인하기

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

3) Registration ID를 등록하고 SDK에 셋팅하기

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
   
  AdFresca.setPushRegistrationId(gcmDeviceId);
     
  AdFresca adfresca = AdFresca.getInstance(this);
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
        String appName = context.getString(R.string.app_name);
        int icon = R.drawable.icon;
        long when = System.currentTimeMillis();

        // 수신 받은 notification을 status bar에 표시합니다.
        // notification에 URI Schema가 설정된 경우, 해당 URI를 실행하며 기본적으로는 targetClass에 설정한 액티비티를 실행하여 줍니다. 
        AdFresca.showNotification(context, intent, MainActivity.class, appName, icon, when);
      } 

    }

    @Override
    protected void onError(Context context, String registrationId) {

    }
  }
```

5) GCMReceiver 클래스 구현하기 (Optional)

만약 GCMIntentService 클래스의 패키지 위치가 프로젝트의 최상위 패키지와 같지 않다면 GCMReceiver 클래스를 직접 구현해야 합니다. GCMReceiver 클래스를 생성하여 아래와 같이 getGCMIntentServiceClassName() 메소드를 구현합니다.

```java
public class GCMReceiver extends GCMBroadcastReceiver { 
   	@Override
	protected String getGCMIntentServiceClassName(Context context) { 
		return "your_app_package.GCMIntentService"; 
	} 
}
```

그리고 AndroidMenefest.xml 파일의 내용을 아래와 같이 수정합니다.

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

Notification 객체의 내용을 일부 수정하고 싶을 경우 사용합니다.

**Example**: Notification 도착 시 사용자 기기에 기본 알람 사운드, 진동, LED 적용하기

```java
public class GCMIntentService extends GCMBaseIntentService {
	@Override
	protected void onMessage(Context context, Intent intent) {
		if (AdFresca.isFrescaNotification(intent)) {
			String appName = context.getString(R.string.app_name);
			int icon = R.drawable.icon;
			long when = System.currentTimeMillis();
						
			AFPushNotification notification = AdFresca.generateAFPushNotification(context, intent, MainActivity.class, appName, icon, when);
			notification.setDefaults(Notification.DEFAULT_ALL); // requires VIBRATE permission
			AdFresca.showNotification(notification);
		}
	}
}
```
Vibrate(진동) 모드를 사용하기 위하여 별도의 AndroidManifest.xml 파일에 퍼미션 등록이 필요합니다.
```xml
<uses-permission android:name="android.permission.VIBRATE"></uses-permission>
```

**Example**: Notification 도착 시 특정 사운드 파일 재생하기

```java
public class GCMIntentService extends GCMBaseIntentService {
	@Override
	protected void onMessage(Context context, Intent intent) {
		if (AdFresca.isFrescaNotification(intent)) {
			String appName = context.getString(R.string.app_name);
			int icon = R.drawable.icon;
			long when = System.currentTimeMillis();
			Uri soundUri = Uri.parse("android.resource://com.adfresca.demo/raw/mysound");
						
			AFPushNotification notification = AdFresca.generateAFPushNotification(context, intent, MainActivity.class, appName, icon, when);
			notification.setSound(soundUri);
			AdFresca.showNotification(notification);
		}
	}
}
```

### Image Notification

_AD fresca_ Android SDK는 일반적인 텍스트 형태의 Notification 뿐만 아니라 이미지를 포함한 새로운 형태의 _Image Push Notification_ 기능을 제공하고 있습니다. 이미지 푸시 메시지를 이용하시면 기존의 텍스트 푸시 메시지에 비해 사용자의 주목을 끌 수 있을 뿐만 아니라, 한 눈에 쉽게 내용을 파악할 수 있습니다.

<center>
<img src="https://adfresca.zendesk.com/attachments/token/ordowzyitlmzvmn/?name=image_push_v1+copy.png" height=500/>&nbsp;
<img src="https://adfresca.zendesk.com/attachments/token/5okygnwkprh58k5/?name=image_push_v4+copy.png" height=500/>
</center>

AD fresca의 Image Push Notification은 사용자 디바이스 상태에 따라 2가지 유형의 템플릿으로 표시됩니다.

1. 디바이스가 잠금 상태일 때 표시되는 전면 레이어 형태 (왼쪽 스크린샷)
2. 디바이스가 활성화 상태일 때 표시되는 Android Notification 형태 (오른쪽 스크린샷)

스마트폰 사용 중에 전면 레이어로 이미지를 노출하는 경우, 사용자에게 부담감을 줄 수 있습니다. 그래서 화면이 잠겨 있지 않은 상태에서는 Android Notification을 이용해서 이미지와 텍스트를 함께 노출 시킴으로써 보다 자연스러운 커뮤니케이션을 할 수 있도록 구현되어 있습니다.

전면 레이어 형태의 메시지뷰가 표시되지 않는 보다 자세한 경우는 아래와 같습니다.
- 사용자의 디바이스가 잠금 상태가 아닌 경우
- 전화가 걸려오고 있는 경우 (이미 메시지 뷰가 표시된 상황에서 전화가 오는 경우는 뷰가 자동으로 닫합니다.)
- 캠페인에 설정된 로컬 이미지 리소스가 애플리케이션 빌드에 포함되지 않은 경우
- 구 버전 SDK가 적용되어 있는 경우

위 4가지 경우에는 Android Notification 형태의 메시지 뷰가 디바이스에 표시됩니다.

_Image Push Notification_ 기능을 적용하기 위해서는 아래의 과정이 필요합니다.

1) AndroidMenefest.xml 퍼미션 정보 확인하기
``` xml
....
    <!-- If you need a push notification feature, add following permissions -->
    <permission android:name="com.adfresca.demo.permission.C2D_MESSAGE" android:protectionLevel="signature" />
	<uses-permission android:name="com.adfresca.demo.permission.C2D_MESSAGE" />
	<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
	<uses-permission android:name="android.permission.GET_ACCOUNTS" />
	<uses-permission android:name="android.permission.WAKE_LOCK" />
	<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
	<uses-permission android:name="android.permission.READ_PHONE_STATE" /> 
	<uses-permission android:name="android.permission.VIBRATE" />
....
```

2) 이미지 파일 준비하기

현재 메시지 뷰에 표시되는 이미지 리소스는 애플리케이션 빌드에 포함된 파일이름을 대쉬보드에서 지정하여 적용됩니다. (이후 서비스 업데이트를 통해 대쉬보드에서 별도로 등록한 이미지를 내려받아 표시하는 기능이 추가됩니다.)

AD fresca Android SDK는 애플리케이션 빌드의 'assets', 'res/drawable', 'res/raw' 폴더에 위치한 이미지 파일을 검색하여 표시하고 있습니다. 원하는 이미지 파일을 해당 위치에 저장하여 빌드합니다.

FHD (1080 * 1920) 해상도의 단말기 기준으로 권장하는 이미지 사이즈 리스트는 아래와 같습니다.
- 464px * 464px (1:1 비율의 이미지를 사용할 경우)
- 800px * 464px (가로 형태의 이미지를 사용할 경우)
- 464px * 800px (세로 형태의 이미지를 사용할 경우)

지정된 파일을 원본 비율에 맞추어 아래와 같이 Overlay 형태의 메시지뷰에 표시할 수 있습니다.
<center>
<img src="https://adfresca.zendesk.com/attachments/token/ordowzyitlmzvmn/?name=image_push_v1+copy.png" width=200/>&nbsp;
<img src="https://adfresca.zendesk.com/attachments/token/jzehtdeatza0nde/?name=image_push_v2+copy.png" width=200/>&nbsp;
<img src="https://adfresca.zendesk.com/attachments/token/lo9ngjyz663um41/?name=image_push_v3+copy.png" width=200/>
</center>

**주의:** Android Notification 형태의 뷰는 현재 Android UI에서 제공하는 [BigPictureStyle](http://developer.android.com/reference/android/app/Notification.BigPictureStyle.html) 설정을 적용하여 Notification 영역에 표시되고 있습니다. OS 4.1 버전부터 지원되며 OS에서 화면 해상도에 맞게 표시할 이미지 사이즈를 지정하여 표시하게 됩니다. 이 과정에서 세로 길이가 긴 형태의 이미지들은 이미지가 가운데로 크롭될 가능성이 높습니다. 

3) AdFresca.showNotification() 메소드 확인하기

Image Push Notification 기능은 showNotification() 메소드를 통해 메시지를 표시하는 경우에만 동작합니다. 위의 가이드 내용처럼 showNotification() 메소드가 호출되고 있는지 확인합니다.

```java
...
    protected void onMessage(Context context, Intent intent) {
      if (AdFresca.isFrescaNotification(intent)) { 
        String appName = context.getString(R.string.app_name);
        int icon = R.drawable.icon;
        long when = System.currentTimeMillis();

        AdFresca.showNotification(context, intent, MainActivity.class, appName, icon, when);
      } 
...
```

### Baidu Push Service

_AD fresca_ Android SDK는 Google의 GCM 서비스 외에도 Baidu Push 서비스를 이용하여 푸쉬 메시지를 사용자에게 전송할 수 있습니다. 

SDK를 적용하기 이전에 ["Baidu Cloud Push" ](http://developer.baidu.com/wiki/index.php?title=docs/cplat/push)가이드 문서를 읽어보시길 권장합니다.

1) Baidu Push SDK 설치하기
- Baidu에서 제공하는 [Baidu Push Android SDK](http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk) 를 다운로드 받습니다.
- /libs/pushservice.jar 파일을 프로젝트에 복사하여 설치합니다.
    
2) AndroidManifest.xml 내용 추가하기

```xml
<manifest>   
  <application>
      .........
       <!-- Baidu push service -->
        <activity android:name="com.adfresca.ads.AdFrescaPushActivity" /> 
        
        <receiver android:name="YOUR_PACKAGE.BaiduPushMessageReceiver">    <!-- Baidu Push Notification을 처리하기 위해 직접 구현하는 내용입니다 -->
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
                <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
                <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
            </intent-filter>
        </receiver>
        
        <receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
                <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
                <action android:name="com.baidu.android.pushservice.action.media.CLICK" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>                   
        </receiver>
        
        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1" />        
        <!-- push service end -->
   </application>
    ..........
	<!-- Baidu Push permissions -->
	<uses-permission android:name="android.permission.READ_PHONE_STATE" /> 
	<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" /> 
	<uses-permission android:name="android.permission.BROADCAST_STICKY" /> 
	<uses-permission android:name="android.permission.WRITE_SETTINGS" /> 
	<uses-permission android:name="android.permission.VIBRATE" />
	<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
	<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/> 
	<uses-permission android:name="android.permission.DISABLE_KEYGUARD" /> 
	<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" /> 
	<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />    
    ..........
</manifest>
```

3) 최초 Activity에서 Baidu Push service 시작하기

```java
  // Start Baidu push service with Baidu push API Key 
  PushManager.startWork(getApplicationContext(),
				PushConstants.LOGIN_TYPE_API_KEY, 
				"YOUR_BAIDU_PUSH_API_KEY");

  AdFresca adfresca = AdFresca.getInstance(this);
  adfresca.startSession();
```

4) BaiduPushMessageReceiver 클래스 구현하기

```java
public class BaiduPushMessageReceiver extends BroadcastReceiver {

	@Override
	public void onReceive(final Context context, Intent intent) {
		if (intent.getAction().equals(PushConstants.ACTION_MESSAGE)) {
			if (AdFresca.isFrescaNotification(intent)) {
	            String appName = context.getString(R.string.app_name);
	            int icon = R.drawable.icon;
	            long when = System.currentTimeMillis();

	            AdFresca.showNotification(context, intent, DemoIntroActivity.class, appName, icon, when);

		} else if (intent.getAction().equals(PushConstants.ACTION_RECEIVE)) {
			AdFresca.handleBaiduPushRegistration(intent);
		}
	}
}
```

Baidu Push 적용이 완료되었습니다. 푸쉬 메시지의 알람 사운드 추가, BigViewStyle 적용 등은 GCM 적용과 마찬가지로 [Custom Notification](#custom-notification) 내용에 따라 적용이 가능합니다.

* * *

## Custom URL

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
위와 같이 설정한 경우, Campaign의 _Click URL_ 값을 myapp://com.adfresca.zone?item=abc 으로 설정하여 DemoZoneActivity가 바로 실행되도록 할 수 있습니다.

함께 넘어온 파라미터 (item=abc) 값을 얻기 위해서는 DemoZoneActivity를 아래와 같이 구현합니다.

```java
public void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);

  Uri uri = getIntent().getData();
  if (uri != null && uri.getScheme().equals("myapp")) { 
    String item = uri.getQueryParameter("item");
  }
}
```

### Cocos2d-x 환경에서 Custom URL 사용하기

액티비티를 페이지 개념으로 사용하는 네이티브 환경과 달리, Cocos2d-x나 Unity와 같은 엔진을 사용하여 안드로이드 애플리케이션을 개발하는 경우 단 하나의 액티비티만을 사용하며 엔진 내부적으로 페이지를 처리합니다.

때문에 위와 같이 schema를 지정할 수 있는 액티비티의 제약이 생깁니다. MAIN 으로 지정된 액티비티는 url schema를 적용할 수 없습니다. 

그래서 아래와 같은 방법들을 사용하여 Custom URL을 처리합니다.

1) Main 액티비티의 startActivity(intent) 메소드를 오버라이딩하여 Custom URL 처리하기 (Annoucnement 캠페인)

Annoucnement 캠페인을 통해 전달되는 Click URL은 항상 인게임 상황에서 전달되며, SDK가 내부적으로 startActivity() 메소드를 이용하여 호출하고 있습니다. 이러한 조건에서는 게임이 실행되고 있는 Main 액티비티의 startActivity() 메소드를 직접 구현함으로써 Custom URL 처리가 가능합니다. 아래와 같이 코드를 구현하면 'myapp://' 형식의 Custom URL이 전달 될 시에 새로운 액티비티를 호출하지 않고 직접 처리할 수 있습니다.

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
(위 방법을 적용할 때에는 AndroidMenefest.xml 파일을 별도로 설정하지 않고 모든 값을 코드로 처리합니다.)

2) Push Notification을 통해 넘어오는 Custom URL 처리하기 (Push Notificiaton 캠페인)

Custom URL이 설정된 Push Notification을 수신한 경우, Notification을 터치 시 원하는 액션을 지정할 수 있습니다. 단, 이 경우는 인게임 상황이 아니기 때문에 조금 다른 방법을 사용합니다.

먼저 PushProxyActivity 라는 이름의 액티비티 클래스를 하나 생성합니다. 그리고 AndroidMenefest.xml 내용을 아래와 같이 추가합니다. 

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
위와 같이 설정한 경우 Push Notificaiton 캠페인에서는 myapp://com.adfresca.push?item=abc 와 같은 형식의 url을 입력해야 합니다.

다음은 PushProxyActivity 클래스의 내용을 구현해야 합니다. PushProxyActivity 클래스는 Android OS로 부터 수신하는 Custom URL 정보를 받아 처리하고 바로 자신을 종료하는 단순한 프록시 형태의 액티비티입니다. 만약 현재 게임이 실행 중이 아니라면 Custom URL을 처리할 수 없으므로 새로 게임을 시작하며 uri 값을 넘겨야 합니다.

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
마지막으로 PushProxyActivity를 통해 게임이 실행된 경우 넘어오는 uri 값을 처리합니다. Main 액티비티에 아래와 같은 내용을 추가합니다.

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

Cocos2d-x 환경에서 Custom URL을 처리할 수 있는 모든 방법을 구현하였습니다.

* * *

## In-App Purchase Tracking (Beta)

_**(현재 In-App-Purchase Tracking 기능은 SDK 2.4.0-beta 버전에서만 지원됩니다.)**_

_In-App-Purchase Tracking_  기능을 통하여 현재 앱에서 발생하고 있는 모든 인-앱 결제를 분석하고 캠페인 타겟팅에 이용할 수 있습니다.

AD fresca의 In-App-Purchase Tracking은 2가지 유형이 있습니다.

1. 실제 화폐를 통해 결제되는 Actual Item Purchase Tracking (예: USD $1.99를 결제하여 Gold 100개 아이템을 구입)
2. 가상 화폐를 통해 결제되는 Virtual Item Purchase Tracking (예: Gold 10개를 이용하여 포션 아이템을 구입)

위 2가지 유형의 데이터를 모두 Tracking 함으로써 앱의 매출뿐만 아니라 인-앱 사용자들의 아이템 구매 추이 분석까지 가능합니다.

아이템 정보 등록을 위한 별도의 작업은 필요하지 않으며, 클라이언트에서 결제된 아이템 정보가 자동으로 대쉬보드에 등록되는 방식입니다. (아이템 리스트 확인은 대쉬보드 'Overview' 메뉴의 Settings - In App Items 페이지를 통해 확인할 수 있습니다.)

아래의 적용 예제를 참고하여 간단히 In-App-Purchase Tracking 기능을 적용합니다.

### Actual Item Tracking

Actual Item의 결제는 각 앱스토어별 인-앱 결제 라이브러리를 통해 이루어집니다. 각 결제 라이브러리에서 _'결제 성공'_ 이벤트가 발생 할 시에 AFPurchase 객체를 생성하고 logPurchase(purchase) 메소드를 호출합니다.

적용 예제: Google Play 결제 
```java
// Callback for when a purchase is finished
IabHelper.OnIabPurchaseFinishedListener mPurchaseFinishedListener = new IabHelper.OnIabPurchaseFinishedListener() {
	public void onIabPurchaseFinished(IabResult result, Purchase purchase) {
		Log.d(TAG, "Purchase finished: " + result + ", purchase: " + purchase);

		if (mHelper == null || result.isFailure() || !verifyDeveloperPayload(purchase)) {
			......
			return;
		}

		Log.d(TAG, "Purchase successful.");
		if (purchase.getPurchaseState() == 0) {
			SkuDetails detail = currentInventory.getSkuDetails(purchase.getSku());
	        	
			String itemId = purchase.getSku(); // Sku value or any unique value of purchased item
			String currencyCode = "KRW"; // The currencyCode must be specified in the ISO 4217 standard. (ex: USD, KRW, JPY)
			Double price =  parsePrice(detail.getPrice()); // For Google Play, you can get the price value from SkuDetails
			Date purhcaseDate = new Date(purchase.getPurchaseTime());
			String orderId = purchase.getOrderId();
			String receiptData = purchase.getOriginalJson();
			String signature = purchase.getSignature();

			AFPurchase actualPurchase = new AFPurchase.Builder(AFPurchase.Type.ACTUAL_ITEM)
													  .setItemId(itemId)
													  .setCurrencyCode(currencyCode)
													  .setPrice(price)
													  .setPurchaseDate(purhcaseDate)
													  .setReceipt(orderId, receiptData, signature)
													  .build();

			AdFresca.getInstance(MainActivity.this).logPurchase(actualPurchase);
		}
		
		......
    }
};
```

위 예제는 Google Play 결제 라이브러리를 기준으로 작성되었지만 아마존이나 티스토어 등 모든 결제 라이브러리에서도 AFPurchase 객체에 필요한 값을 얻어올 수 있습니다.

Actual Item을 위한 AFPurchase.Builder의 보다 자세한 설명은 아래와 같습니다.

Method | Description
------------ | ------------- | ------------
setItemId(string) | 결제한 아이템의 고유 식별 아이디를 설정합니다. 등록된 앱스토어에 상관 없이 앱내에서 고유한 식별 값을 이용하는 것을 권장합니다. AD fresca 대쉬보드에서 해당 값을 기준으로 아이템 목록이 생성됩니다. 
setCurrencyCode(string) | ISO 4217 표준 코드를 설정합니다. Google Play의 경우 'Default price' 에 설정되는 Currency Code 값을 이용하며 타 결제 라이브러리의 경우는 보통 이용 가능한 Currency Code가 고정되어 있습니다 (예: 아마존은 USD, 티스토어는 KRW). 또는 자체 백엔드 서버에서 결제하는 아이템의 Currency Code를 내려받아 설정할 수 있습니다.
setPrice(double) | 아이템의 가격을 설정합니다. 결제 라이브러리에서 주는 값을 이용하거나, 자체 백엔드 서버에서 가격을 내려받아 설정할 수 있습니다. 
setPurchaseDate(date) | 결제된 시간을 Date 객체 형태로 설정합니다. 값이 설정되지 않은 경우 AD fresca 서비스에 기록되는 시간이 결제 시간으로 자동 설정됩니다.
setReceipt(string, string, string) | 추후 Receipt Verficiation 기능을 위해 필요한 데이터를 설정합니다. 현재 버전의 SDK는 Google Play만 지원하며 타 결제 라이브러리의 경우는 값을 설정하지 않습니다.

### Virtual Item Tracking

Virtual Item의 결제는 앱 내의 가상 화폐로 아이템을 결제한 경우를 의미합니다. 앱 내에서 가상 화폐를 이용한 결제 이벤트가 성공한 경우 아래 예제와 같이 AFPurchase 객체를 생성하고 logPurchase(purchase) 메소드를 호출합니다.

적용 예제: 
```java
public void onVirtualItemPurchased(Item item, Date purchasedDate) {
	AFPurchase virtualPurchase = new AFPurchase.Builder(AFPurchase.Type.VIRTUAL_ITEM)
									.setItemId(item.getId()) // "long_sword"
									.setCurrencyCode(item.getCurrencyCode()) // "gold"
									.setPurchaseDate(purchaseDate) // Date object or null
									.setPrice(item.getPrice()) // 10
									.build();
	
	AdFresca.getInstance(this).logPurchase(virtualPurchase);
}
```

Virtual Item을 위한 AFPurchase.Builder의 보다 자세한 설명은 아래와 같습니다.

Method | Description
------------ | ------------- | ------------
setItemId(string) | 결제한 아이템의 고유 식별 아이디를 설정합니다. 등록된 앱스토어에 상관 없이 앱내에서 고유한 식별 값을 이용하는 것을 권장합니다. AD fresca 대쉬보드에서 해당 값을 기준으로 아이템 목록이 생성됩니다. 
setCurrencyCode(string) | 결제에 사용한 가상화폐 고유 코드를 설정합니다. (예: gold)
setPrice(double) | 가상 화폐로 결제한 가격 정보를 설정합니다. (예: gold 10개의 경우 10 값을 설정)
setPurchaseDate(date) | 결제된 시간을 Date 객체 형태로 설정합니다. 값이 설정되지 않은 경우 AD fresca 서비스에 기록되는 시간이 결제 시간으로 자동 설정됩니다.

### IAP Trouble Shooting

logPurchase() 메소드를 통해 기록된 AFPurchase 객체는 AD fresca 서비스에 업데이트되어 실시간으로 대쉬보드에 반영됩니다. 현재까지 등록된 아이템 리스트는 'Overview' 메뉴의 Settings - In App Items 페이지를 통해 확인할 수 있습니다.

만약 아이템 리스트가 새로 갱신되지 않는 경우, AFPurchaseExceptionListener 구현하여 혹시 에러가 발생하고 있지 않은지 확인해야 합니다. 

만약 AFPurchase 객체의 값이 제대로 설정되지 않은 경우, AFPurchaseExceptionListener 통하여 에러 메시지를 표시하고 있으니 아래와 같이 코드를 적용하여 로그를 확인합니다.

```java
......
AdFresca.getInstance(this).logPurchase(purchase, new AFPurchaseExceptionListener(){
	public void onException(AFPurchase purchase, AFException e) {
		Log.e(TAG, (purchase == null ? "purchase=null" : purchase.toString()));
		Log.e(TAG, e.getMessage());
	}
});
```

* * *

## CPI Identifier

Incentivized CPI & CPA 캠페인 기능을 사용하여, 사용자가 Media App에서 Advertising App의 광고를 보고 앱을 설치하였을 때 보상으로 Media App의 아이템을 지급할 수 있습니다.

- Medial App: 다른 앱의 광고를 노출하고, 광고 대상의 앱을 설치한 사용자들에게 보상을 지급하는 앱
- Advertising: Media App에 광고가 노출되는 앱.

Incentivized CPI & CPA 캠페인에 대한 보다 자세한 설명 및 [Dashboard](https://admin.adfresca.com) 사이트에서의 설정 방법은 [크로스 프로모션 캠페인 관리하기](https://adfresca.zendesk.com/entries/22033960) 가이드를 참고하여 주시기 바랍니다.

SDK 적용을 위해서는 Advertising App에서의 패키지 이름 확인 및 Media App에서의 Reward Item 지급 기능을 구현해야 합니다.

#### Advertising App 설정하기:

  Android 플랫폼의 경우 앱의 패키지 이름을 이용하여 광고를 노출한 앱이 실제로 디바이스에 설치되었는지 검사하게 됩니다. 따라서 Advertising App 앱의 패키지 이름을 확인하고 CPI Identifier로 사용합니다.

  (현재 Incentivized CPI 캠페인을 진행할 경우, Advertising App의 SDK 설치는 필수가 아니며 URL Schema 설정만 진행되면 됩니다. 하지만 Incentivized CPA 캠페인을 진행할 경우 반드시 SDK 설치 및 [Marketing Event](#marketing-event) 기능이 적용되어야 합니다.)
  
  AndroidManifest.xml 파일을 열어 패키지 이름을 확인합니다.

  ```xml
  <?xml version="1.0" encoding="utf-8"?>

  <manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.adfresca.demo">
    <application>
    .....
    </application>
  </manifest>
  ```

  위 경우 [Dashboard](https://admin.adfresca.com) 사이트에서 Advertising App의 CPI Identifier 값을 'com.adfresca.demo' 으로 설정하게 됩니다. 

  그리고, Incentivized CPA 캠페인을 진행할 경우는 아래와 같이 보상 조건으로 지정한 마케팅 이벤트가 발생되어야 합니다.
    
  ```java
  // 튜토리얼 완료 이벤트를 보상 조건으로 지정한 경우
  AdFresca adfresca = AdFresca.getInstance(this);     
  adfresca.load(EVENT_INDEX_TUTORIAL); 
  adfresca.show(EVENT_INDEX_TUTORIAL);
  ```
#### Media App SDK 적용하기:

  Media App에서 보상 지급 여부를 확인하고, 사용자에게 아이템을 지급하기 위해서는 SDK 가이드의 [Reward Item](#reward-item) 항목의 내용을 구현합니다.

## Reward Item

Reward Item 기능을 적용하여 현재 사용자에게 지급 가능한 보상 아이템이 있는지 검사하고, 보상 아이템을 사용자에게 지급할 수 있습니다.

Annoucnement 캠페인의 'Reward Item' 항목을 설정했거나, Incentivized CPI & CPA 캠페인의 'Incentive Item' 을 설정한 경우 사용자에게 보상 아이템이 지급됩니다.

먼저 AndroidManifest.xml 내용을 확인합니다.

```xml
<manifest>   
  <application>
      .........
      <activity android:name="com.adfresca.sdk.reward.AFRewardActivity" />
      .........
   </application>
</manifest>
```

이제 구현을 위해서 아래 2가지 코드를 이용합니다.
- checkRewardItems 메소드 호출: 현재 지급 가능한 보상 아이템이 있는지 검사합니다. 사용자가 앱을 실행할 호출하는 것을 권장합니다.
- AFRewardItemListener 구현: 아이템 지급 조건이 만족되면 onReward 이벤트가 발생됩니다. 인자로 넘어온 아이템 정보를 이용하여 사용자에게 아이템을 지급합니다.

```java
@Override
public void onResume() {
  super.onResume();

  AdFresca.setRewardItemListener(new AFRewardItemListener(){
      @Override
      public void onReward(AFRewardItem item) {
        String logMessage = String.format("You got the reward item! (%s)", item.getName());
        Log.d(TAG, logMessage);
        
        // 아이템 고유 값 'uniqueValue'을 이용하여 사용자에게 아이템 지급
        sendItemToUser(item.getUniqueValue());  
      }});
          
  AdFresca adfresca = AdFresca.getInstance(this);
  adfresca.checkRewardItems();
}
```

캠페인 종류에 따라 onReward 이벤트의 발생 조건이 다릅니다.

- Annoucnement 캠페인: 캠페인이 앱 사용자에게 매칭되어 노출될 때 이벤트가 발생합니다
- Incentivized CPI 캠페인: 사용자의 Advertising App 설치가 확인된 후 이벤트가 발생합니다.
- Incentivized CPA 캠페인: 사용자의 Advertising App 설치가 확인되고 보상 조건으로 지정된 마케팅 이벤트가 호출된 후에 발생합니다.

만일 디바이스의 네트워크 단절이 발생한 경우 SDK는 데이터를 로컬에 보관하여 다음 앱 실행에서 아이템 지급이 가능하도록 구현되어 있기 때문에 항상 100% 지급을 보장합니다.

(기존의 getAvailableRewardItems 메소드는 Deprecated 상태로 변경되었지만, 호환성을 보장하여 정상적으로 동작하고 있습니다.)

* * *

## Custom Banner

_(Custom Banner 기능은 Android Platform에 한하여 적용 가능합니다.)_

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
    android:layout_height="wrap_content"
    adfresca:image_size_index="1"
    adfresca:keep_aspect_ratio="width"
    adfresca:default_image="@drawable/default_banner" />
```

- `adfresca:image_size_index="1"` 이미지 사이즈 인덱스를 설정합니다.
- `adfresca:keep_aspect_ratio="width"` 로드된 컨텐츠에 따라 _Banner View_의 가로세로 비율을 유지합니다. _width_가 세팅된 경우 _Banner View_의 세로값을 변경하여 비율을 유지합니다. 이 경우 `android:layout_height`는 반드시 `wrap_content` 가 되어야합니다. (`adfresca:keep_aspect_ratio`는  [ _none_ | _width_ | _height_ ] 중에 하나의 값을 가지며 디폴트는 _none_ 입니다.)
- `adfresca:default_image="@drawable/default_image"` 이미지가 로드되기 전 표시할 디폴트 이미지를 지정합니다.

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

## Advanced Features

### AFLoadListener

AFLoadListener는 [Custom Parameter](#custom-parameter), [In-App-Purchase](#in-app-purchase) 등을 편리하게 업데이트 할 수 있도록 합니다. `AFLoadListener.onStart()`는 `AdFresca.load()`가 호출될 때 마다 호출되기 때문에 다음과 같이 `AFLoadListener`를 구현하고 설정하면 언제든지 최신 값으로 컨텐츠를 로드할 수 있습니다.

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

`AFShowListener`는 SDK에서 컨텐츠 프로세싱이 종료되었을 때 이벤트 처리를 위한 _이벤트 리스너_입니다.

컨텐츠 프로세싱이 종료되었다는 것은 다음 3가지를 의미합니다.

1. 컨텐츠가 정상적으로 화면에 보여지고 닫혀진 경우
2. 컨텐츠가 매칭되지 않았거나 컨텐츠에 맞는 view를 찾을 수 없어서 화면에 보여지지 않고 끝난 경우
3. 네트워크 이슈로 컨텐츠 매칭 요청 시간 초과 (Timeout) 이벤트가 발생한 경우

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

### Google Referrer Tracking

Google Play 캠페인을 통해 앱을 설치하는 경우, Referrer 정보를 분석하여 통계 데이터를 제공합니다.

Referrer 정보를 추출하여 SDK에 설정하기 위하여 아래와 같이 적용 및 테스트 합니다.

1) AndroidManefest.xml에 Reciever 등록 여부 확인하기

Reciever를 등록하여 Google Play 앱을 통해 전달되는 Referrer 값을 자동으로 SDK에 적용합니다.

```xml
<receiver android:name="com.adfresca.sdk.referer.AFRefererReciever" android:exported="true">
  <intent-filter>
          <action android:name="com.android.vending.INSTALL_REFERRER" />
      </intent-filter>
</receiver>
```

2) ADB를 이용하여 테스트 진행하기

디바이스를 연결한 후, 터미널 애플리케이션을 열어 adb shell을 실행합니다. (adb는 Android SDK 설치 디렉토리 아래 platform-tools 디렉토리에 위치)

그리고 아래와 같이 INSTALL_REFERRER 메시지를 디바이스에 전송할 수 있습니다. 패키지 명을 설정하고, referrer 값을 지정합니다.
(referrer 값의 각 파라미터 내용은 [Google Play - Campaign Parameters](https://developers.google.com/analytics/devguides/collection/android/v2/campaigns#campaign-params) 가이드에서 자세한 내용을 확인할 수 있습니다.)

```sh
am broadcast -a com.android.vending.INSTALL_REFERRER -n YOUR_PACKAGE/com.adfresca.sdk.referer.AFRefererReciever --es "referrer" "utm_source=test_source&utm_medium=test_medium&utm_term=test_term&utm_content=test_content&utm_campaign=test_name"
```
3) referrer 값이 SDK에 설정 되었는지 확인하기

```java
  AdFresca adfresca = AdFresca.getInstance(this);
  Log.v(TAG, "Google Referrer = " + adfresca.getReferrer());
``` 
(Advanced) 이미 INSTALL_REFERRER를 추출하는 다른 boradcast recicever를 적용 중인 경우, setReferrer(string) 메소드를 이용하여 직접 SDK에 값을 전달할 수 있습니다.

주의: 특정 디바이스에서 한 번 AD fresca 서비스에 INSTALL_REFERRER가 등록되었다면, 더이상 수동으로 값을 변경할 수 없습니다. 클라이언트에서 값을 변경하더라도 Dashboard의 통계 데이터에는 그 값이 변경되지 않습니다.

* * *

## Proguard Configuration

Proguard 툴을 이용하여 APK 파일을 보호하는 경우 몇 가지 예외 처리 작업을 진행해야 합니다. AD fresca SDK와 SDK에 포함된 OpenUDID 및 Google Gson에 대한 예외 처리를 아래와 같이 적용합니다.

```java
-keep class com.adfresca.** {*;} 
-keep class com.google.gson.** {*;} 
-keep class org.openudid.** {*;} 
-keep class sun.misc.Unsafe { *; }
-keepattributes Signature 
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

* * *

## Release Notes

- **v2.4.0-beta4 _(2014/04/06 Updated)_**
    - v2.3.4에서 적용된 'Announcement 캠페인을 통한 Reward Item 지급 기능'을 지원합니다.
    -  v2.3.4에서 적용된 Incentivized CPA 캠페인 기능을 지원합니다. 자세한 내용은 [CPI Identifier](#cpi-identifier) 항목을 참고하여 주세요.
    - v2.3.4에서 개선된 [Reward Item](#reward-item) 기능이 적용되었습니다. 
- v2.4.0-beta3 
    - v2.3.3에서 적용된 [Image Push Notification](#image-notification) 기능이 추가되었습니다. 
- v2.4.0-beta2 
    - v2.3.2에서 패치된 Timeout 이벤트 처리가 적용되었습니다.
    - [Unity Plugin 2.2.0-beta1](https://github.com/adfresca/sdk-unity-sample/blob/master/README.md#release-notes) 버전을 지원합니다.
- v2.4.0-beta1
    - 앱 내에서 발생하는 In-App Purchase 데이터를 트랙킹할 수 있는 기능이 추가되었습니다. 자세한 내용은 [In-App Purchase Tracking (Beta)](#in-app-purchase-tracking-beta) 항목을 참고하여 주세요. [In-App Purchase Tracking (Beta)](#in-app-purchase-tracking-beta) 항목을 참고하여 주세요.
- **v2.3.4 _(2014/04/06 Updated)_**
   - Announcement 캠페인을 통한 Reward Item 지급 기능을 지원합니다.
   - Incentivized CPA 캠페인 기능을 지원합니다. 자세한 내용은 [CPI Identifier](#cpi-identifier) 항목을 참고하여 주세요.
   - AFRewardItemListener 구현 기능이 추가되어, 지급 가능한 아이템이 발생할 시에 자동으로 onReward 이벤트가 발생합니다. 보다 자세한 내용은 [Reward Item](#reward-item) 항목을 참고하여 주세요.
- v2.3.3 _(01/30/2014 Updated)_ 
    - Image Push Notifcaiton 기능이 추가되었습니다. 적용에 대한 자세한 내용은 [Image Notification](#image-notification) 항목을 참고하여 주세요.
    - showNotification() 메소드를 통해 표시되는 푸시 메시지에 [BigTextStyle](http://developer.android.com/reference/android/app/Notification.BigTextStyle.html)이 기본적으로 적용됩니다.
- v2.3.2 
    - load() 메소드를 호출한 후 지정된 요청 시간이 초과된 경우 (Timeout), AFShowListener 리스너의 onFinish() 이벤트가 발생하도록 수정되었습니다. onFinish() 이벤트 발생에 대한 설명은 [AFShowListener](#afshowlistener) 항목을 참고하여 주세요.
- v2.3.1
    - GCM Registration ID가 새로 등록되거나 변경 시, SDK가 ID값을 실시간으로 AD fresca 서비스에 업데이트하도록 개선되었습니다. (기존에는 앱 실행 시에만 업데이트하였습니다.)
- v2.3.0 
    - AD fresca SDK에서 Baidu Push를 이용할 수 있도록 지원합니다. 자세한 내용은 [Baidu Push Service](#baidu-push-service) 항목을 참고하여 주세요.
- v2.2.3
    - Push Notification 캠페인에서 설정한 title, ticker 메시지가 표시될 수 있도록 지원합니다.
    - `AdFresca.generateNotification` 메소드가 Deprecated 되었습니다. `AdFresca.generateAFPushNotification()` 메소드를 사용합니다.
- v2.2.2
    - 로컬 캐시 기능이 개선되었습니다.
- v2.2.1 
    -  'Close Mode' 기능을 지원합니다. Dashboard에서 Interstitial View의 닫힘 설정을 제어할 수 있습니다.
- v2.2.0 
    - [Google Referrer Tracking](#google-referrer-tracking) 기능이 추가 되었습니다. 
    - `AdFresca.setCustomParameter` 메소드가 deprecated 되었습니다. AdFresca 객체의 `setCustomParameterValue()` 메소드를 사용해 주세요.
    - `AdFresca.setInAppPurchaseCount` 메소드가 deprecated 되었습니다. AdFresca 객체의 `setNumberOfInAppPurchases()` 메소드를 사용해 주세요.
    - `setCustomParameterValue()` 메소드에서 64 bit integer를 지원합니다. (long type)
    -  Custom Parameter 및 In-App Purchase 정보를 로컬에 저장하여 사용합니다.
- v2.1.3 
    - 테스트 모드를 이용하여 여러 개의 Incentivized Campaign을 동시에 테스트하지 못하는 버그가 수정되었습니다.
- v2.1.2
    - `AFBannerView.setKeepAspectRatio(AFBannerView.KeepAspectRatio)` 메소드가 추가되었습니다. 자바코드에서 `keep_aspect_ratio`를 세팅할 수 있습니다.
- v2.1.1
    - _Banner View_ 컨텐츠의 가로세로 비율을 유지하기 위한 `keep_aspect_ratio` attribute 가 `AFBannerView`에 추가되었습니다.
    - `AFRewardItem` 의 멤버변수들이 Deprecated 되었습니다. 각 멤버변수들의 Getter 를 사용해주시기 바랍니다. 멤버변수들은 private 제한자로 변경될 예정입니다.
- v2.1.0
    - `getAvailableRewardItems()`, `checkRewardItems()`, `checkRewardItems(boolean)` 메소드가 추가 되었습니다. _Incentivized Campaign_을 통해 유저에게 Reward Item 을 지급 할 수 있습니다.
- v2.0.0
    - `AdExceptionListner`, `AdException` 클래스가 Deprecated 되었습니다. `AFExceptionListner`, `AFException` 을 사용해주세요.
- v2.0.0-beta.1
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
