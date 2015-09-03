## Contents
- [Basic Integration](#basic-integration)
  - [Installation](#installation)
  - [Start Session](#start-session)
  - [In-App Messaging](#in-app-messaging)
  - [Push Messaging](#push-messaging)
  - [Test Device Registration](#test-device-registration)
- [IAP, Reward and Sales Promotion](#iap-reward-and-sales-promotion)
  - [In-App Purchase Tracking](#in-app-purchase-tracking)
  - [Give Reward](#give-reward)
  - [Sales Promotion](#sales-promotion)
- [Dynamic Targeting](#dynamic-targeting)
  - [Custom Parameter](#custom-parameter)
  - [Marketing Moment](#marketing-moment)
- [Advanced](#advanced)
  - [Custom Banner (Android Only)](#custom-banner)
  - [AFShowListener](#afshowlistener)
  - [Timeout Interval](#timeout-interval)
- [Reference](#reference)
  - [Deep Link](#deep-link)
  - [Cross Promotion Configuration](#cross-promotion-configuration)
  - [Google Referrer Tracking](#google-referrer-tracking)
  - [Image Push Notification](#image-push-notification)
  - [Baidu Push Service Integration](#baidu-push-service-integration)
  - [Proguard Configuration](#proguard-configuration)
- [Troubleshooting](#troubleshooting)
- [Release Notes](#release-notes)

* * *

## Basic Integration

### Installation

아래 링크를 통해 SDK 파일을 다운로드 합니다.

[Android SDK Download](http://file.adfresca.com/distribution/sdk-for-Android.zip)

[Android SDK Download without Gson Library](http://file.adfresca.com/distribution/sdk-for-Android-wihtout-gson.zip)

SDK를 프로젝트에 설치하기 위하여 아래의 과정을 진행합니다.

1) **AdFresca.jar** 파일은 **lib** 폴더에, **adfresca_attr.xml** 파일은 **res/values** 폴더에 각각 복사합니다.

<img src="https://adfresca.zendesk.com/attachments/token/bja88u9zake4knm/?name=add_adfresca_jar_and_attr_xml.png" width="300"/>

2) 프로젝트의 Build Path를 설정합니다.
- 프로젝트를 오른쪽 클릭 후, **Properties** 메뉴를 클릭합니다.
- 좌측 **Java Build Path** 메뉴에서 **Libraries** 탭을 들어간 후, **Add JARs** 버튼을 클릭하여 **AdFresca.jar** 파일을 추가합니다.

<img src="https://adfresca.zendesk.com/attachments/token/ogcnzf3kmyzbcvg/?name=add_jar.png" width="600" />

3) **AndroidManifest.xml** 수정하기
  ```xml
  <manifest package="your.app.package">
    <application>
      <activity/>
      
      <!-- Service for OpenUDID -->
      <service android:name="org.openudid.OpenUDID_service">
        <intent-filter>
          <action android:name="org.openudid.GETUDID" />
        </intent-filter>
      </service>

      <!-- Activity for Reward -->
      <activity android:name="com.adfresca.sdk.reward.AFRewardActivity" />
     
      <!-- Boradcast Receiver for Google Referrer Tracking-->
      <receiver android:name="com.adfresca.sdk.referer.AFRefererReciever" android:exported="true">
        <intent-filter>
          <action android:name="com.android.vending.INSTALL_REFERRER" />
        </intent-filter>
      </receiver>
    </application>
    
    <!-- Permissions -->
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
  </manifest>
  ```

### Start Session

이제 SDK 적용을 시작하기 위해 몇 가지 간단한 코드를 적용합니다. 첫 번째로 API Key를 설정하고 앱의 실행을 기록하는 startSession() 메소드를 적용합니다. API Key는 [Dashboard](https://dashboard.nudge.do) 사이트에서 등록한 앱을 선택한 후 Overview 메뉴의 Settings - API Keys 버튼을 클릭하여 확인이 가능합니다.

startSession() 메소드를 앱이 최초로 실행되는 액티비티에 적용합니다. 앱이 실행되는 동안 반복적으로 호출되지 않도록 주의해야 합니다.

```java
protected void onCreate(Bundle savedInstanceState) {
  ....
  AdFresca.setApiKey(API_KEY);
  AdFresca.getInstance(this).startSession();
}
```

### In-App Messaging

인-앱 메시징 기능을 이용하여, 사용자에게 원하는 메시지를 실시간으로 전달할 수 있습니다. 메시지를 전달하고자 하는 시점에 load(), show() 메소드만을 호출하여 적용이 가능합니다. 메시지는 전면 interstitial 이미지, 텍스트, 혹은 iframe 웹페이지 형태로 화면에 표시될 수 있습니다. 메시지는 현재 플레이 중인 사용자가 인-앱 메시징 캠페인의 조건과 매칭된 경우에만 화면에 표시됩니다. 조건에 만족하는 캠페인이 없다면 사용자는 아무런 화면을 보지 않고 자연스럽게 플레이를 이어갑니다. 매칭과 관련한 인-앱 메시징의 다이나믹 타겟팅 기능은 아래의 [Dynamic Targeting](#dynamic-targeting) 항목에서 보다 자세히 설명하고 있습니다.

```java
protected void onCreate(Bundle savedInstanceState) {
  ...
  AdFresca fresca = AdFresca.getInstance(this);
  fresca.load();
  fresca.show();
}
```

첫 번째로 인-앱 메시징 코드를 적용한 경우, 아래와 같이 테스트 이미지 메시지가 표시됩니다. 해당 이미지를 터치하면 앱스토어 페이지로 이동합니다. 현재 보고 있는 테스트 메시지는 이후 테스트 모드 설정을 변경하여 더이상 보이지 않도록 설정하게 됩니다.

<img src="https://adfresca.zendesk.com/attachments/token/zngvftbmcccyajk/?name=device-2013-03-18-133517.png" width="240" />
&nbsp;
<img src="https://adfresca.zendesk.com/attachments/token/phn4fcpvbi2damx/?name=device-2013-03-18-133443.png" height="240" />
* * *

### Push Messaging

푸시 메시징 기능을 이용하여 사용자가 앱을 실행하지 않을 때에도 언제든 메시지를 전달할 수 있습니다. 아래의 과정을 통하여 푸시 메시징 기능을 적용합니다.

SDK를 적용하기 이전에 [Google API Console](https://cloud.google.com/console) 사이트에서 프로젝트를 생성하고, [Dashboard](https://dashboard.nudge.do) 사이트에 설정할 GCM API Key 및 SDK 적용에 필요한 GCM_SENDER_ID (Project Number) 값을 얻어야 합니다.

'[Android Push Notification 설정 및 적용하기 (GCM)](https://adfresca.zendesk.com/entries/28526764)' 가이드를 참고하여 필요한 값들을 얻습니다.

1) GCM Helper Library 설치 확인하기
  - GCM 서비스를 이용하기 위해서는 구글에서 제공하는 GCM Client 라이브러리가 설치되어 있어야 합니다. 
  - 기존에 GCM 라이브러리가 설치되어 있지 않는 경우, 구글에서 제공하는 [GCM Helper Library](http://code.google.com/p/gcm/source/browse/) 를 다운로드 받습니다. /gcm-client/dist 폴더에 포함된 **gcm.jar** 파일을 프로젝트에 복사합니다.
    
2) AndroidManifest.xml 내용 추가하기

```xml
<manifest>   
  <application>
      .........
      <receiver android:name="YOUR.PACKAGE.NAME.GCMReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">  
        <intent-filter>
          <action android:name="com.google.android.c2dm.intent.RECEIVE" />
          <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
          <category android:name="YOUR.PACKAGE.NAME" />
         </intent-filter>
      </receiver>
      <service android:name="YOUR.PACKAGE.NAME.GCMIntentService" />  

      <activity android:name="com.adfresca.ads.AdFrescaPushActivity" />
      ..........
   </application>
    ..........
    <permission android:name="YOUR.PACKAGE.NAME.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="YOUR.PACKAGE.NAME.permission.C2D_MESSAGE" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.VIBRATE" />
    ..........
</manifest>
```

- GCMReceiver 클래스와 GCMIntentService 클래스는 이미 적용 중인 내용이 있다면 그대로 사용하여 SDK 코드만 추가합니다. 
- 만약 기존에 사용 중인 GCM 클래스가 없다면, 샘플로 제공되는 [GCMReceiver](https://gist.github.com/sunku/29906033dcee764ef022) 및 [GCMIntentService](https://gist.github.com/sunku/05c5e4feb3d0fb8d4088) 소스코드를 참고하여 클래스를 작성합니다.

3) GCM Registration ID 설정하기

```java
AdFresca fresca = AdFresca.getInstance(this);
fresca.setPushRegistrationIdentifier("GCM_REGISTRATION_ID_OF_THIS_DEVICE");
```

- 기존에 GCM Registration ID를 등록하고 가져오는 부분을 구현하지 않았다면, '[How to Get GCM Registration ID](https://gist.github.com/sunku/b47eecee77afe40aa515)' 내용을 참고하여 코드를 추가할 수 있습니다.

4) GCMIntentService 구현하기

```java
protected void onRegistered(Context context, String registrationId) {
 AdFresca.handlePushRegistration(registrationId);
}

protected void onUnregistered(Context context, String registrationId) {
  AdFresca.handlePushRegistration(null);
}

protected void onMessage(Context context, Intent intent) {
  // Check if this notification is from Nudge
  if (AdFresca.isFrescaNotification(intent)) {

    Class<?> targetActivityClass = YourMainActivity.class;
    String appName = context.getString(R.string.app_name);
    int icon = R.drawable.icon;
    long when = System.currentTimeMillis();

    // Show this message
    AFPushNotification notification = AdFresca.generateAFPushNotification(context, intent, targetActivityClass, appName, icon, when);
    notification.setDefaults(Notification.DEFAULT_ALL); 
    AdFresca.showNotification(notification);
  }
}
```
- 푸시 메시지에 Deep Link가 포함되어 있다면, 사용자가 메시지 터치 시에 SDK는 해당 URL을 실행합니다.
- 푸시 메시지에 Deep Link가 없다면, SDK는 targetActivityClass를 실행하여 앱을 실행합니다. 
- notification.setSound(uri) 메소드를 이용하면 메시지 수신 시에 사운드 파일을 재생할 수도 있습니다.

### Test Device Registration

Nudge는 테스트 모드 기능을 지원하여 테스트를 원하는 디바이스에만 원하는 메시지를 전달할 수 있습니다. 이로 인해 SDK가 적용된 앱이 이미 앱스토어에 출시된 경우, 게임 운영팀 혹은 개발팀에게만 새로운 메시지를 전달하여 테스트할 수 있도록 지원합니다.

테스트 기기 등록을 위한 아이디 값은 SDK를 통해 추출이 가능하며 2가지 방법을 지원 합니다.
 
1. getTestDeviceId() 메소드를 사용하여 로그로 출력하는 방법
  - After connecting your device with ADB, you can simply print out test device ID with a logger.

  ```java
  AdFresca fresca = AdFresca.getInstance(this);
  Log.d(TAG, "Nudge Test Device ID is = " + fresca.getTestDeviceId());
  ```

2. setPrintTestDeviceId() 메소드를 사용하여 콘텐츠 뷰에 기기 아이디를 화면에 표시하는 방법
  - 개발자가 기기를 직접 연결할 수 없는 경우, 설정을 활성화 한 상태로 앱 빌드를 전덜하여 설치합니다. 화면에 표시된 기기 아이디를 직접 기록하여 등록할 수 있습니다.
  - 담당 마케터가 원격에서 근무하는 경우 해당 기능을 유용하게 사용할 수 있습니다.
  - 설정이 활성화된 상태로 앱이 배포되지 않도록 주의해야 합니다.

  ```java
  AdFresca fresca = AdFresca.getInstance(this);
  fresca.setPrintTestDeviceId(true);
  fresca.load();
  fresca.show();
  ```

테스트 디바이스 아이디를 확인한 이후에는, [Dashboard](https://dashboard.nudge.do)를 접속하여 'Test Device' 메뉴를 통해 디바이스 등록이 가능합니다.

* * *

## IAP, Reward and Sales Promotion

### In-App Purchase Tracking

_**(현재 In-App-Purchase Tracking 기능은 SDK 2.4.0 버전에서만 지원됩니다.)**_

_In-App-Purchase Tracking_  기능을 통하여 현재 앱에서 발생하고 있는 모든 인-앱 결제를 분석하고 캠페인 타겟팅에 이용할 수 있습니다.

Nudge의 In-App-Purchase Tracking은 2가지 유형이 있습니다.

1. 실제 화폐를 통해 결제되는 Hard Currency Item Purchase Tracking (예: USD $1.99를 결제하여 Gold 100개 아이템을 구입)
2. 가상 화폐를 통해 결제되는 Soft Currency Item Purchase Tracking (예: Gold 10개를 이용하여 포션 아이템을 구입)

위 2가지 유형의 데이터를 모두 Tracking 함으로써 앱의 매출뿐만 아니라 인-앱 사용자들의 아이템 구매 추이 분석까지 가능합니다.

아이템 정보 등록을 위한 별도의 작업은 필요하지 않으며, 클라이언트에서 결제된 아이템 정보가 자동으로 대쉬보드에 등록되는 방식입니다. (아이템 리스트 확인은 대쉬보드 'Overview' 메뉴의 Settings - In App Items 페이지를 통해 확인할 수 있습니다.)

아래의 적용 예제를 참고하여 간단히 In-App-Purchase Tracking 기능을 적용합니다.

#### Hard Currency Item Tracking

Hard Currency Item의 결제는 각 앱스토어별 인-앱 결제 라이브러리를 통해 이루어집니다. 각 결제 라이브러리에서 _'결제 성공'_ 이벤트가 발생 할 시에 AFPurchase 객체를 생성하고 logPurchase(purchase) 메소드를 호출합니다. 그리고 _'결제 실패'_ 이벤트가 발생 할 시에는 cancelPromotionPurchase() 메소드를 호출합니다.

적용 예제: Google Play 결제 
```java
// Callback for when a purchase is finished
IabHelper.OnIabPurchaseFinishedListener mPurchaseFinishedListener = new IabHelper.OnIabPurchaseFinishedListener() {
  public void onIabPurchaseFinished(IabResult result, Purchase purchase) {
    Log.d(TAG, "Purchase finished: " + result + ", purchase: " + purchase);

    if (mHelper == null || result.isFailure() || !verifyDeveloperPayload(purchase)) {
      ......
      AdFresca.getInstance(MainActivity.this).cancelPromotionPurchase();
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

      AFPurchase hardPurchase = new AFPurchase.Builder(AFPurchase.Type.HARD_ITEM)
                            .setItemId(itemId)
                            .setCurrencyCode(currencyCode)
                            .setPrice(price)
                            .setPurchaseDate(purhcaseDate)
                            .setReceipt(orderId, receiptData, signature)
                            .build();

      AdFresca.getInstance(MainActivity.this).logPurchase(hardPurchase);
    }
    
    ......
    }
};
```

위 예제는 Google Play 결제 라이브러리를 기준으로 작성되었지만 아마존이나 티스토어 등 모든 결제 라이브러리에서도 AFPurchase 객체에 필요한 값을 얻어올 수 있습니다.

Hard Currency Item을 위한 AFPurchase.Builder의 보다 자세한 설명은 아래와 같습니다.

Method | Description
------------ | ------------- | ------------
setItemId(string) | 결제한 아이템의 고유 식별 아이디를 설정합니다. 등록된 앱스토어에 상관 없이 앱내에서 고유한 식별 값을 이용하는 것을 권장합니다. Nudge 대쉬보드에서 해당 값을 기준으로 아이템 목록이 생성됩니다. 
setCurrencyCode(string) | ISO 4217 표준 코드를 설정합니다. Google Play의 경우 'Default price' 에 설정되는 Currency Code 값을 이용하며 타 결제 라이브러리의 경우는 보통 이용 가능한 Currency Code가 고정되어 있습니다 (예: 아마존은 USD, 티스토어는 KRW). 또는 자체 백엔드 서버에서 결제하는 아이템의 Currency Code를 내려받아 설정할 수 있습니다.
setPrice(double) | 아이템의 가격을 설정합니다. 결제 라이브러리에서 주는 값을 이용하거나, 자체 백엔드 서버에서 가격을 내려받아 설정할 수 있습니다. 
setPurchaseDate(date) | 결제된 시간을 Date 객체 형태로 설정합니다. 값이 설정되지 않은 경우 Nudge 서비스에 기록되는 시간이 결제 시간으로 자동 설정됩니다.
setReceipt(string, string, string) | 추후 Receipt Verficiation 기능을 위해 필요한 데이터를 설정합니다. 현재 버전의 SDK는 Google Play만 지원하며 타 결제 라이브러리의 경우는 값을 설정하지 않습니다.

#### Soft Currency Item Tracking

Soft Currency Item의 결제는 앱 내의 가상 화폐로 아이템을 결제한 경우를 의미합니다. 앱 내에서 가상 화폐를 이용한 결제 이벤트가 성공한 경우 아래 예제와 같이 AFPurchase 객체를 생성하고 logPurchase(purchase) 메소드를 호출합니다. 그리고 _'결제 실패'_ 이벤트가 발생 할 시에는 cancelPromotionPurchase() 메소드를 호출합니다.

적용 예제: 
```java
public void onSoftItemPurchased(Item item, Date purchasedDate) {
  AFPurchase softPurchase = new AFPurchase.Builder(AFPurchase.Type.SOFT_ITEM)
                  .setItemId(item.getId()) // "long_sword"
                  .setCurrencyCode(item.getCurrencyCode()) // "gold"
                  .setPurchaseDate(purchaseDate) // Date object or null
                  .setPrice(item.getPrice()) // 10
                  .build();
  
  AdFresca.getInstance(this).logPurchase(softPurchase);
}

// 사용자가 결제를 취소했거나, 실패한 경우
public void onPurchaseSoftItemFailure() {
  AdFresca.getInstance(this).cancelPromotionPurchase();
}
```

Soft Currency Item을 위한 AFPurchase.Builder의 보다 자세한 설명은 아래와 같습니다.

Method | Description
------------ | ------------- | ------------
setItemId(string) | 결제한 아이템의 고유 식별 아이디를 설정합니다. 등록된 앱스토어에 상관 없이 앱내에서 고유한 식별 값을 이용하는 것을 권장합니다. Nudge 대쉬보드에서 해당 값을 기준으로 아이템 목록이 생성됩니다. 
setCurrencyCode(string) | 결제에 사용한 가상화폐 고유 코드를 설정합니다. (예: gold)
setPrice(double) | 가상 화폐로 결제한 가격 정보를 설정합니다. (예: gold 10개의 경우 10 값을 설정)
setPurchaseDate(date) | 결제된 시간을 Date 객체 형태로 설정합니다. 값이 설정되지 않은 경우 Nudge 서비스에 기록되는 시간이 결제 시간으로 자동 설정됩니다.

#### IAP Trouble Shooting

logPurchase() 메소드를 통해 기록된 AFPurchase 객체는 Nudge 서비스에 업데이트되어 실시간으로 대쉬보드에 반영됩니다. 현재까지 등록된 아이템 리스트는 'Overview' 메뉴의 Settings - In App Items 페이지를 통해 확인할 수 있습니다.

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

### Give Reward

Reward 지급 기능을 적용하여 현재 사용자에게 지급 가능한 보상 아이템이 있는지 검사하고, 보상 아이템을 사용자에게 지급할 수 있습니다.

Reward 캠페인에서 'Reward Item' 항목을 설정하거나, Incentivized CPI & CPA 캠페인의 'Incentive Item' 을 설정한 경우 사용자에게 보상 아이템이 지급됩니다.

먼저 AndroidManifest.xml 내용을 확인합니다.

```xml
<manifest>   
  <application>
      <activity android:name="com.adfresca.sdk.reward.AFRewardActivity" />
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
        String logMessage = String.format("You got the reward item! (%s)", item.toJson());
        Log.d(TAG, logMessage);
        
        // 아이템 정보를 이용하여 현재 사용자에게 아이템을 지급합니다.
        sendItemToUser(currentUserId, item.getUniqueValue(), item.getQuantity(), item.getSecurityToken());		
      }});
          
  AdFresca fresca = AdFresca.getInstance(this);
  fresca.checkRewardItems();
}
```

캠페인 종류에 따라 onReward 이벤트의 발생 조건이 다릅니다.

- Reward 캠페인: 캠페인이 앱 사용자에게 매칭되어 노출될 때 이벤트가 발생합니다
- Incentivized CPI 캠페인: 사용자의 Advertising App 설치가 확인된 후 이벤트가 발생합니다.
- Incentivized CPA 캠페인: 사용자의 Advertising App 설치가 확인되고 보상 조건으로 지정된 마케팅 이벤트가 호출된 후에 발생합니다.

만일 디바이스의 네트워크 단절이 발생한 경우 SDK는 데이터를 로컬에 보관하여 다음 앱 실행에서 아이템 지급이 가능하도록 구현되어 있기 때문에 항상 100% 지급을 보장합니다.

##### sendItemToUser() 메소드의 구현

SDK에서 요청한 아이템을 사용자에게 지급해야 합니다. 클라이언트에서 직접 지급하거나, 백엔드 서버와 통신하여 사용자의 선물함으로 보상을 지급하는 방식을 이용할 수 있습니다. 아이팀의 고유 값, 수량, 그리고 시큐리티 토큰값을 이용하여 현재 로그인된 사용자에게 아이템을 지급합니다.

##### 백엔드 서버를 통해 아이템을 지급할 경우 보안 이슈 해결하기

저희 SDK에서는 특정 사용자가 동일한 캠페인에서 1회 이상 아이템이 지급되지 않도록 처리하고 있습니다. 하지만 클라이언트에서 백엔드 서버와 통신하는 과정이 노출된다면 외부 공격에 의해 아이템이 중복으로 지급되는 보안 이슈가 발생할 수도 있습니다. 해당 문제를 방지하기 위하여 시큐리티 토큰값을 제공하고 있습니다. 시큐리티 토큰값은 대쉬보드에서 캠페인 생성 시에 자동으로 생성 혹은 직접 지정할 수 있는 고유 값입니다. 해당 값을 이용하여 아래와 같은 처리를 할 수 있습니다.

1. 백엔드 서버에서는 진행하려는 리워드 캠페인의 시큐리티 토큰값을 데이터베이스에 미리 보관하여, 존재하지 않는 토큰값이 포함된 요청을 거절합니다.
2. 특정 사용자가 동일한 토큰값으로 1회 이상 지급 요청을 하는 경우 요청을 거절합니다. 
3. 만약 토큰값이 외부에 노출되었다고 판단될 경우, 대쉬보드에서 토큰값을 새로 생성하거나 수정합니다.

* * *

### Sales Promotion

Sales Promotion 캠페인을 이용하여 특정 아이템의 구매를 유도할 수 있습니다. 사용자가 캠페인에 노출된 이미지 메시지를 클릭할 경우 해당 아이템의 결제 UI가 표시됩니다. SDK는 사용자의 실제 결제 여부까지 자동으로 트랙킹하여 대쉬보드에서 실시간으로 통계를 제공합니다. 

프로모션 기능을 적용하기 위해서 AFPromotionListener를 구현합니다. 프로모션 캠페인이 노출된 후 사용자가 이미지 메시지의 액션 영역을 탭하면 onPromotion() 이벤트가 발생합니다. 이벤트에 넘어오는 promotionPurchase 객체 정보를 이용하여 사용자에게 아이템 결제 UI를 표시하도록 코드를 적용합니다.

Hard Currency 아이템의 경우 인-앱 결제 라이브러리를 이용하여 결제 UI를 표시합니다. promotionPurchase 객체의 ItemId 값이 아이템의 SKU 값에 해당됩니다. 아래의 예제는 구글 플레이의 결제 라이브러리 코드를 이용하고 있습니다.

Soft Currency 아이템의 경우는 앱이 기존에 사용하고 있는 상점 내 아이템 결제 UI를 표시하도록 코드를 작성합니다. Soft Currency 프로모션의 경우는 2가지 가격 할인 옵션을 제공하고 있습니다. getDiscountType() 메소드를 이용하여 할인 옵션을 확인할 수 있습니다.

1. **Discount Price**: 캠페인에 직접 지정된 가격으로 아이템을 판매합니다. getPrice() 메소드를 이용하여 가격 정보를 얻습니다.
2. **Discount Rate**: 캠페인에 지정된 할인율을 적용하여 아이템을 판매합니다. getDiscountRate() 메소드를 이용하여 할인율 정보를 받아옵니다.



```java
AdFresca.setPromotionListener(new AFPromotionListener(){
  @Override
  public void onPromotion(AFPurchase promotionPurchase) {
    String itemId = promotionPurchase.getItemId();
    String logMessage = "no logMessage";
        
    if (promotionPurchase.getCurrencyType().getType() == AFPurchase.Type.HARD_ITEM.getType()) {
      // Using Google Play In-app Billing Library   
      iabHelper.launchPurchaseFlow(MainActivity.this, promotionPurchase.getItemId(), 0, yourPurchaseFinishedListener, "YOUR_PAYLOAD");
      
      logMessage = String.format("on HARD_ITEM Promotion (%s)", itemId);  
      
    } else if (promotionPurchase.getCurrencyType().getType() == AFPurchase.Type.SOFT_ITEM.getType()) {          
      String currencyCode = promotionPurchase.getCurrencyCode();
          
      if (promotionPurchase.getDiscountType() == AFPurchase.DiscountType.DISCOUNTED_TYPE_PRICE) {
        // Use a discounted price
        double discountedPrice = promotionPurchase.getPrice(); 
      
        showPurchaseUIWithDiscountedPrice(itemId, currencyCode, discountedPrice);
        
        logMessage = String.format("on SOFT_ITEM Promotion (%s) with %.2f %s", promotionPurchase.getItemName(), discountedPrice, currencyCode);    
        
      } else if (promotionPurchase.getDiscountType() == AFPurchase.DiscountType.DISCOUNT_TYPE_RATE) {
        // Use this rate to calculate a discounted price of item. discountedPrice = originalPrice - (originalPrice * discountRate)
        double discountRate = promotionPurchase.getDiscountRate(); 
        
        showPurchaseUIWithDiscountRate(itemId, currencyCode, discountRate);
        
        logMessage = String.format("on SOFT_ITEM Promotion (%s) with %.2f %% discount", promotionPurchase.getItemName(), discountRate * 100.0);
      }
    }
    Log.d(TAG, logMessage);
  }}
); 
```

SDK가 사용자의 실제 구매 여부를 트랙킹하기 위해서는 [In-App Purchase Tracking](#in-app-purchase-tracking) 기능이 미리 구현되어 있어야 합니다. 특히 사용자가 아이템을 구매를 하지 않거나 실패한 경우를 트랙킹 하기 위하여 cancelPromotionPurchase() 메소드가 반드시 적용되어 있어야 합니다.

* * *

## Dynamic Targeting

### Custom Parameter

커스텀 파라미터는 마케팅 목적으로 사용자를 분류하기 위해 사용하는 속성을 말하며 마케터가 임의로 정의할 수 있습니다. (예. 사용자의 레벨, 스테이지, 플레이 횟수 등) 커스텀 파라미터를 이용하면 사용자의 특정 속성에 따라 세그먼트를 정의하고 실시간으로 모니터링할 수 있습니다. 또한 캠페인 실행 시에는 보다 더 정교한 타겟팅을 통해 높은 성과를 거둘 수 있습니다. (Nudge SDK에서 자동적으로 수집하는 단말 ID, 기본 언어, 국가, 앱 버전 등의 정보는 커스텀 파라미터로 설정할 필요가 없습니다.)

Nudge SDK는 트랙킹하려고 하는 커스텀 파라미터의 유형에 따라 아래 2가지 방법을 제공합니다.

- 현재 상태 값을 트랙킹하는 경우
  - 특정 속성의 현재 상태 값을 트랙킹할 때 사용합니다. 
  - 예) 레벨, 최종 스테이지, 친구수 등
  - 사용 코드: **setCustomParameterValue** 메소드를 이용하여 현재 상태 값 (Integer, Boolean 지원)을 SDK에 전달합니다.

- 특정 이벤트의 횟수를 트랙킹하는 경우
  - 특정 이벤트마다 증가하는 횟수를 트랙킹할 때 사용합니다.
  - 예) 플레이 횟수, 가챠 이용 횟수 등
  - 사용 코드: **incrCustomParameterValue** 메소드를 이용하여 이벤트 발생시 증가된 횟수 (Integer)을 SDK에 전달합니다.

먼저 커스텀 파라미터를 특정할 수 있는 문자열 형태의 Unique Key 값을 정합니다. (예: "level", "facebook_flag", "play_count") 앱을 처음 실행하는 시점 또는 사용자가 로그인하는 시점에 커스텀 파라미터의 현재 상태 값을 설정합니다.

```java
public void onCreate() {
  AdFresca fresca = AdFresca.getInstance(this);     
  fresca.setCustomParameterValue("level", User.level);
  fresca.setCustomParameterValue("facebook_flag", User.hasFacebookAccount);
  fresca.startSession();
}
```

커스텀 파라미터의 값이 변경되는 시점 (이벤트 발생 시)에 상태 값을 갱신하거나 횟수를 증가시킵니다.
  
```java
public void onUserLevelChanged(int level) {  
  AdFresca fresca = AdFresca.getInstance(this);     
  fresca.setCustomParameterValue("level", level);
}

public void onGameFinished {
  AdFresca fresca = AdFresca.getInstance(this);     
  fresca.incrCustomParameterValue("play_count", 1);
}
```

위와 같이 코딩을 마치고 앱을 실행하면 SDK는 저장한 커스텀 파라미터 값을 Nudge 서버로 전송합니다.  Nudge 서버는 활성화된 커스텀 파라미터의 값만을 저장하기 때문에 반드시 [Dashboard](https://dashboard.nudge.do)에 접속하여 해당 커스텀 파라미터를 활성화 (Activate)해야 합니다.

<img src="https://s3-ap-northeast-1.amazonaws.com/file.adfresca.com/guide/sdk/custom_parameter_index.png">

 Overview 메뉴 -> Settings - Custom Parameters 버튼을 클릭하면 커스텀 파라미터 목록이 표시됩니다. 해당 커스텀 파라미터의 Unique Key를 찾은 다음, 이름 ('Name')을 입력하고 활성화 (Activate)합니다.

#### Stickiness Custom Parameter

Stickiness 커스텀 파라미터는 사용자의 stickiness를 측정하기 위해 사용하는 특수한 형태의 커스텀 파라미터입니다. 예를 들어 사용자의 플레이 횟수를 커스텀 파라미터로 지정하고 Stickiness 커스텀 파라미터로 설정하면 해당 사용자의 '오늘 총 플레이 횟수', '최근 7일간의 총 플레이 횟수', '최근 7일간의 일평균 플레이 횟수' 등을 세그먼트 필터로 사용할 수 있습니다. Stickiness 커스텀 파라미터를 이용하면 사용자들의 충성도 (또는 몰입도)에 따라 세그먼트를 나누고 모니터링할 수 있습니다.

Stickiness 커스텀 파라미터로 사용하고자 하는 커스텀 파라미터는 반드시 **incrCustomParameterValue** 메소드를 이용해야 합니다.  Stickiness 커스텀 파라미터를 사용하고자 하는 경우 해당 커스텀 파라미터를 활성화한 후 support@nudge.do 로 메일 주시기 바랍니다.

* * *

### Marketing Moment

마케팅 모멘트는 유저에게 메세지를 전달하고자 하는 상황을 의미합니다. (예: 캐릭터 레벨 업, 퀘스트 달성, 스토어 페이지 진입)

마케팅 모멘트 기능을 사용하여 지정된 상황에 알맞는 캠페인이 노출되도록 할 수 있습니다.

마케팅 모멘트 설정은 [Dashboard](https://dashboard.nudge.do) 사이트를 접속하여 앱의 Overview 메뉴 -> Settings - Marketing Moments 버튼을 클릭하여 확인할 수 있습니다.

SDK 적용을 위해서는 Dashboard에서 지정된 각 마케팅 모멘트의 '인덱스' 값이 필요합니다. 인덱스 값은 1,2,3,4 와 같은 Integer 형태의 고유 값이며 소스코드에 Constant 형태로 지정하여 이용하는 것을 권장합니다.

각 모멘트 발생 시, load() 메소드에 원하는 모멘트 인덱스 값을 인자로 넘겨주시면 간단히 적용이 완료됩니다.

(load() 메소드에 인덱스를 설정하지 않은 경우, 인덱스 값은 '1' 값이 자동으로 지정됩니다.)

**Example**:  사용자가 메인 페이지로 이동할 시에 설정한 콘텐츠를 노출

```java
  public class MainPageActivity extends Activity {
    public void onCreate(Bundle savedInstanceState) {
      AdFresca fresca = AdFresca.getInstance(this);     
      fresca.load(MOMENT_INDEX_MAIN_PAGE);  // 메인 페이지에 설정한 콘텐츠츠를 노출
      fresca.show(MOMENT_INDEX_MAIN_PAGE);
    }
  }
```

**Example**: 사용자의 게임 캐릭터가 레벨업을 했을 때 설정한 콘텐츠를 노출

```java
  public void onUserLevelChanged(int level) {
    AdFresca fresca = AdFresca.getInstance(this);
    fresca.setCustomParameterValue(CUSTOM_PARAM_INDEX_LEVEL, level); // 사용자 level 정보를 가장 최신으로 업데이트
    fresca.load(MOMENT_INDEX_LEVEL_UP); // 레벨업 모멘트에 설정한 콘텐츠를 노출
    fresca.show(MOMENT_INDEX_LEVEL_UP);
  }
```

* * *

## Advanced

### Custom Banner

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

#### Floating View

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

#### Banner View

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
- `adfresca:keep_aspect_ratio="width"` 로드된 콘텐츠츠에 따라 _Banner View_의 가로세로 비율을 유지합니다. _width_가 세팅된 경우 _Banner View_의 세로값을 변경하여 비율을 유지합니다. 이 경우 `android:layout_height`는 반드시 `wrap_content` 가 되어야합니다. (`adfresca:keep_aspect_ratio`는  [ _none_ | _width_ | _height_ ] 중에 하나의 값을 가지며 디폴트는 _none_ 입니다.)
- `adfresca:default_image="@drawable/default_image"` 이미지가 로드되기 전 표시할 디폴트 이미지를 지정합니다.

**Example:** 한 액티비티에서 기본 _Interstitial View_와 _Banner View_ 두 개의 View를 동시에 사용하기

마케팅 모멘트 기능을 활용하여 여러 개의 뷰를 한 화면에 동시에 노출할 수 있습니다. 

```java
protected void onCreate(Bundle savedInstanceState) {
  AdFresca fresca = AdFresca.getInstance(this);
  fresca.load(MOMENT_INDEX_MAIN_PAGE_FOR_BANNER); // 메인 페이지진입 시 Banner View 를 위한 콘텐츠츠를 load 합니다.
  fresca.load(MOMENT_INDEX_MAIN_PAGE_FOR_INTERSTITIAL); // 메인 페이지 진입 시 Interstitial View 를 위한 콘텐츠츠를 load 합니다.
  fresca.show(); // load 된 모든 콘텐츠츠를 show 합니다.
}
```

* * *

### Baidu Push Service Integration

_Nudge_ Android SDK는 Google의 GCM 서비스 외에도 Baidu Push 서비스를 이용하여 푸쉬 메시지를 사용자에게 전송할 수 있습니다. 

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

  AdFresca fresca = AdFresca.getInstance(this);
  fresca.startSession();
```

4) BaiduPushMessageReceiver 클래스 구현하기

```java
public class BaiduPushMessageReceiver extends BroadcastReceiver {

  @Override
  public void onReceive(final Context context, Intent intent) {
    if (intent.getAction().equals(PushConstants.ACTION_MESSAGE)) {
      if (AdFresca.isFrescaNotification(intent)) {
        Class<?> targetActivityClass = YourMainActivity.class;
        String appName = context.getString(R.string.app_name);
        int icon = R.drawable.icon;
        long when = System.currentTimeMillis();

        // 푸시 메시지를 표시합니다. 
        AFPushNotification notification = AdFresca.generateAFPushNotification(context, intent, targetActivityClass, appName, icon, when);
        notification.setDefaults(Notification.DEFAULT_ALL); 
        AdFresca.showNotification(notification);
      }
    } else if (intent.getAction().equals(PushConstants.ACTION_RECEIVE)) {
      AdFresca.handleBaiduPushRegistration(intent);
    }
  }
}
```

Baidu Push 적용이 완료되었습니다.

* * *

### AFShowListener

`AFShowListener`는 SDK에서 콘텐츠츠 프로세싱이 종료되었을 때 이벤트 처리를 위한 _이벤트 리스너_입니다.

콘텐츠츠 프로세싱이 종료되었다는 것은 다음 3가지를 의미합니다.

1. 콘텐츠츠가 정상적으로 화면에 보여지고 닫혀진 경우
2. 콘텐츠츠가 매칭되지 않았거나 콘텐츠츠에 맞는 view를 찾을 수 없어서 화면에 보여지지 않고 끝난 경우
3. 네트워크 이슈로 콘텐츠츠 매칭 요청 시간 초과 (Timeout) 이벤트가 발생한 경우

이 두가지 경우를 `AFShowListener.show(int eventIndex, AFView view)`의 두번째 인자 `view`로 판별할 수 있습니다.

- `view != null`이면 콘텐츠츠가 정상적으로 보여진 경우입니다. 이때 `view`는 [ _Default View_ | _Floating View_ | _Banner View_ ] 가 됩니다. `AFView.isDefaultView()`로 _Default View_ 인지 판별할 수 있습니다.
- `view == null`이면 콘텐츠츠가 보여지지 않고 끝난 경우입니다.

```java
AdFresca fresca = AdFresca.getInstance(this);
fresca.load(MOMENT_INDEX_STAGE_CLEAR);
fresca.show(new AFShowListener(){
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
**Example:** _인트로 액티비티_에서 콘텐츠츠를 보여주고 끝나면 _메인 액티비티_로 이동

```java
AdFresca fresca = AdFresca.getInstance(this);
fresca.load(MOMENT_INDEX_INTRO);
fresca.show(MOMENT_INDEX_INTRO, new AFShowListener(){
  @Override
  public void onFinish(int eventIndex, AFView view) {
    startActivity(new Intent(IntroActivity.this, MainActivity.class));
  }
});
```

**_주의!_**
사용자가 마켓이나 다른 애플리케이션의 URI가 설정된 콘텐츠츠를 클릭한 경우, 화면이 다른 애플리케이션으로 이동할 수 있습니다.

이 때 onFinish()에 다른 Activity 로 이동하도록 구현하였다면, 사용자가 다른 화면에 있는 동안 앱의 Activity 가 미리 이동해버릴 수 있습니다.

아래와 같은 방법으로 해당 문제를 해결할 수 있습니다.

Dashboard 에서 Marketing Moment의 Close Mode 를 Override 로 변경 합니다. 이로 인해 ㅋ텐츠를 클릭해도 콘텐츠츠가 닫히지 않습니다. 그리고 액티비티의 onResume() 이벤트를 아래와 같이 구현합니다.

```java
@Override
public void onResume() {
  super.onResume();

  AdFresca fresca = AdFresca.getInstance(this);
  
  if (fresca.getDefaultViewVisibility() == View.VISIBLE && fresca.isUserClickedDefaultView()) {   
    fresca.closeAd();
  }
}
```

* * *

### Timeout Interval

콘텐츠츠의 최대 로딩 시간을 직접 지정하실 수 있습니다. 지정된 시간 내에 콘텐츠츠가 로딩되지 못한 경우, 사용자에게 콘텐츠츠를 노출하지 않습니다.

최소 1초 이상 지정이 가능하며, 지정하지 않을 시 기본 값으로 5초가 지정 됩니다.

```java
  AdFresca.setTimeoutInterval(5) // # 5 seconds

  AdFresca fresca = AdFresca.getInstance(this);
  fresca.load();
  fresca.show();
```

* * *

## Reference

### Deep Link

캠페인의 Deep Link 설정 시에 Custom URL Schema를 지정할 수 있습니다. 이를 통해 사용자가 인앱 메시지나 푸시 메시지를 탭할 때 자신이 원하는 특정 앱 페이지로 이동하거나 특정 액션을 실행하도록 지정할 수 있습니다.

먼저 AndroidManifest.xml 파일을 수정하여 scheme 정보를 추가해야 합니다.

```xml
<activity android:name=".MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
  <intent-filter> 
    <action android:name="android.intent.action.VIEW" /> 
    <category android:name="android.intent.category.DEFAULT" /> 
    <category android:name="android.intent.category.BROWSABLE" /> 
    <data android:scheme="myapp" android:host="myaction" />
  </intent-filter> 
</activity>
```

위와 같이 설정한 경우, 캠페인의 _Deep Link_ 값을 myapp://myaction?item=abc 으로 설정하여 MainActivity가 바로 실행되도록 할 수 있습니다.

함께 넘어온 파라미터 (item=abc) 값을 얻기 위해서는 MainActivity 아래와 같이 구현합니다.

```java
@Override
public void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);

  Uri uri = getIntent().getData();
  handleUri(uri);
}

@Override
protected void onNewIntent(Intent intent) {
  super.onNewIntent(intent);
  setIntent(intent);

  Uri uri = intent.getData();
  handleUri(uri);
}

private handleUri(Uri uri) {
  if (uri != null && uri.getScheme().equals("myapp")) { 
    String item = uri.getQueryParameter("item");
  }
}
```

#### Cocos2d-x 환경에서 인앱 메시징 Deep Link 처리하기

액티비티를 페이지 개념으로 사용하는 네이티브 환경과 달리, Cocos2d-x나 Unity와 같은 엔진을 사용하여 안드로이드 애플리케이션을 개발하는 경우 단 하나의 액티비티만을 사용하며 엔진 내부적으로 페이지를 처리합니다. 때문에 인앱 메시징을 통해 실행되는 Deep Link를 Cocos2dxActivity를 벗어나지 않고 처리할 수 있도록 추가 작업이 필요합니다. 

Cocos2dxActivity 클래스의 startActivity(intent) 메소드를 아래와 같이 구현합니다.

```java
@Override 
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
    handleUri(uri);
  } 
}
```

* * *

### Cross Promotion Configuration

Incentivized 크로스프로모션 캠페인 기능을 사용하여, 사용자가 Media App에서 Advertising App의 광고를 보고 앱을 설치하였을 때 보상으로 Media App의 아이템을 지급할 수 있습니다.

- Medial App: 다른 앱의 광고를 노출하고, 광고 대상의 앱을 설치한 사용자들에게 보상을 지급하는 앱
- Advertising: Media App에 광고가 노출되는 앱.

Incentivized 크로스프로모션 캠페인에 대한 보다 자세한 설명 및 [Dashboard](https://dashboard.nudge.do) 사이트에서의 설정 방법은 [크로스 프로모션 이해하기](https://adfresca.zendesk.com/entries/22033960) 가이드를 참고하여 주시기 바랍니다.

SDK 적용을 위해서는 Advertising App에서의 패키지 이름 확인 및 Media App에서의 Reward Item 지급 기능을 구현해야 합니다.

#### Advertising App 설정하기:

  Android 플랫폼의 경우 앱의 패키지 이름을 이용하여 광고를 노출한 앱이 실제로 디바이스에 설치되었는지 검사하게 됩니다. 따라서 Advertising App 앱의 패키지 이름을 확인하고 CPI Identifier로 사용합니다.

  (현재 Incentivized CPI 캠페인을 진행할 경우, Advertising App의 SDK 설치는 필수가 아니며 CPI Identifier 설정만 진행하면 됩니다. 하지만 Incentivized CPA 캠페인을 진행할 경우 반드시 SDK 설치 및 [Marketing Moment](#marketing-moment) 기능이 적용되어야 합니다.)
  
  AndroidManifest.xml 파일을 열어 패키지 이름을 확인합니다.

  ```xml
  <manifest package="com.adfresca.demo">
    ...
  </manifest>
  ```

  위 경우 [Dashboard](https://dashboard.nudge.do) 사이트에서 Advertising App의 CPI Identifier 값을 'com.adfresca.demo' 으로 설정하게 됩니다. 

  마지막으로, Incentivized CPA 캠페인을 진행할 경우는 보상 조건으로 지정한 마케팅 모멘트가 발생되어야 합니다. 사용자가 보상 조건을 완료한 이후 아래와 같이 지정한 마케팅 모멘트를 호출합니다.
    
  ```java
  // 튜토리얼 완료 모멘트를 보상 조건으로 지정한 경우
  AdFresca fresca = AdFresca.getInstance(this);     
  fresca.load(MOMENT_INDEX_TUTORIAL); 
  fresca.show(MOMENT_INDEX_TUTORIAL);
  ```

#### Media App SDK 적용하기:

  Media App에서 보상 지급 여부를 확인하고, 사용자에게 아이템을 지급하기 위해서는 SDK 가이드의 [Give Reward](#give-reward) 항목의 내용을 구현합니다.

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
AdFresca fresca = AdFresca.getInstance(this);
Log.v(TAG, "Google Referrer = " + fresca.getReferrer());
``` 
(Advanced) 이미 INSTALL_REFERRER를 추출하는 다른 boradcast recicever를 적용 중인 경우, setReferrer(string) 메소드를 이용하여 직접 SDK에 값을 전달할 수 있습니다.

주의: 특정 디바이스에서 한 번 Nudge 서비스에 INSTALL_REFERRER가 등록되었다면, 더이상 수동으로 값을 변경할 수 없습니다. 클라이언트에서 값을 변경하더라도 Dashboard의 통계 데이터에는 그 값이 변경되지 않습니다.

* * *

### Image Push Notification

_Nudge_ Android SDK는 일반적인 텍스트 형태의 Notification 뿐만 아니라 이미지를 포함한 새로운 형태의 _Image Push Notification_ 기능을 제공하고 있습니다. 이미지 푸시 메시지를 이용하시면 기존의 텍스트 푸시 메시지에 비해 사용자의 주목을 끌 수 있을 뿐만 아니라, 한 눈에 쉽게 내용을 파악할 수 있습니다.

<center>
<img src="https://adfresca.zendesk.com/attachments/token/jzehtdeatza0nde/?name=image_push_v2+copy.png" height=500/>&nbsp;
<img src="https://adfresca.zendesk.com/attachments/token/5okygnwkprh58k5/?name=image_push_v4+copy.png" height=500/>
</center>

Nudge의 Image Push Notification은 사용자 디바이스 상태에 따라 2가지 유형의 템플릿으로 표시됩니다.

1. 디바이스가 잠금 상태일 때 표시되는 전면 레이어 형태 (왼쪽 스크린샷)
2. 디바이스가 활성화 상태일 때 표시되는 Android Notification 형태 (오른쪽 스크린샷)

스마트폰 사용 중에 전면 레이어로 이미지를 노출하는 경우, 사용자에게 부담감을 줄 수 있습니다. 그래서 화면이 잠겨 있지 않은 상태에서는 Android Notification을 이용해서 이미지와 텍스트를 함께 노출 시킴으로써 보다 자연스러운 커뮤니케이션을 할 수 있도록 구현되어 있습니다.

전면 레이어 형태의 메시지뷰가 표시되지 않는 보다 자세한 경우는 아래와 같습니다.
- 사용자의 디바이스가 잠금 상태가 아닌 경우
- 전화가 걸려오고 있는 경우 (이미 메시지 뷰가 표시된 상황에서 전화가 오는 경우는 뷰가 자동으로 닫합니다.)
- 캠페인에 설정된 로컬 이미지 리소스가 애플리케이션 빌드에 포함되지 않은 경우
- 구 버전 SDK가 적용되어 있는 경우

위 4가지 경우에는 Android Notification 형태의 메시지 뷰가 디바이스에 표시됩니다.

_Image Push Notification_ 기능을 적용하기 위해서는 800px * 464px 사이즈의 이미지가 필요합니다. 아래 2가지 방법을 통해 이미지를 준비할 수 있습니다.

1) 대쉬보드에서 이미지를 업로드하는 경우

대쉬보드에서 이미지 파일을 직접 업로드하여 전송할 수 있습니다. SDK는 해당 이미지를 내려받아 표시하기 때문에 아무런 작업이 필요하지 않습니다.

2) 로컬 이미지 리소스를 이용하는 경우

Nudge Android SDK는 애플리케이션 빌드의 'assets', 'res/drawable', 'res/raw' 폴더에 위치한 이미지 파일을 검색하여 표시하고 있습니다. 원하는 이미지 파일을 해당 위치에 저장하여 빌드합니다.

* * *

### Proguard Configuration

Proguard 툴을 이용하여 APK 파일을 보호하는 경우 몇 가지 예외 처리 작업을 진행해야 합니다. Nudge SDK와 SDK에 포함된 OpenUDID 및 Google Gson에 대한 예외 처리를 아래와 같이 적용합니다.

```java
-keep class com.adfresca.** {*;} 
-keep class com.google.gson.** {*;} 
-keep class org.openudid.** {*;} 
-keep class sun.misc.Unsafe { *; }
-keepattributes Signature 
```

* * *


## Troubleshooting

콘텐츠츠 제대로 출력되지 않거나, 에러가 발생한다면 AdExceptionListener 인터페이스를 구현하여, 에러 정보를 확인 할 수 있습니다.

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
- **v2.4.8 _(2015/03/20 Updated)_**
  - [Image Push Notification](#image-push-notification) 전송 시 대쉬보드에 업로드된 이미지를 내려받아 표시하는 기능을 제공합니다.
- v2.4.7
  - [Custom Parameter](#custom-parameter) 설정 시 정수 형태의 고유 인덱스 값이 아닌 문자열 형태의 고유 키 값을 사용할 수 있도록 변경되었습니다. (인덱스를 이용하는 기존 방식도 그대로 지원합니다.)
- v2.4.6
  - hasCustomParameterWithIndex 메소드가 추가되었습니다. 
- v2.4.5
  - AFPurchase 객체에 HARD_ITEM, SOFT_ITEM purchase type이 추가되고 ACTUAL_ITEM, SOFT_ITEM 값이 deprecated 되었습니다. 자세한 내용은 [In-App Purchase Tracking](#in-app-purchase-tracking) 항목을 참고하여 주세요.
  - HARD_ITEM, SOFT_ITEM 값이 추가됨에 따라 [Sales Promotion](#sales-promotion) 항목의 예제 코드가 변경되었습니다. 반드시 getType()을 이용하여 화폐 유형을 검사해야 합니다.
-v2.4.4
  - A/B 테스트 기능을 지원합니다. 해당 기능은 별도의 코딩 작업 없이 이용 가능합니다.
- v2.4.3 
  - Stickiness 커스텀 파리미터의 안드로이드 베타 버전을 지원합니다.
- v2.4.2
    - Sales Promotion 캠페인 기능을 이용하여 Soft Currency 아이템의 프로모션 기능을 지원합니다. 자세한 내용은 [Sales Promotion](#sales-promotion) 항목을 참고하여 주세요.
    - 캠페인 매칭 시에 여러 개의 캠페인이 동시에 매칭될 수 있습니다. 새로운 SDK는 순차적으로 매칭된 캠페인들의 메시지를 표시합니다.
- v2.4.1
    - 리워드 지급 시에 시큐리티 토큰값을 이용하여 보안 이슈를 해결할 수 있습니다. 자세한 내용은 [Give Reward](#give-reward) 항목을 참고하여 주세요.
    - Sales Promotion 캠페인 기능을 이용하여 Hard Currency 아이템의 프로모션 기능을 지원합니다. 자세한 내용은 [Sales Promotion](#sales-promotion) 항목을 참고하여 주세요.
    - [In-App Purchase Tracking](#in-app-purchase-tracking) 기능에서 cancelPromotionPurchase() 메소드가 추가되었습니다. 
    - 이미지 메시지의 Tap Area 기능을 지원합니다.
    - iap beta 버전이 2.4.1부터 통합되었습니다. 
- v2.4.04
    - v2.3.4에서 적용된 '인-앱 메시징 캠페인을 통한 Reward Item 지급 기능'을 지원합니다.
    - v2.3.4에서 적용된 Incentivized CPA 캠페인 기능을 지원합니다. 자세한 내용은 [Cross Promotion Configuration](#cross-promotion-configuration) 항목을 참고하여 주세요.
    - v2.3.4에서 개선된 [Give Reward](#give-reward) 기능이 적용되었습니다. 
- v2.4.03 
    - v2.3.3에서 적용된 [Image Push Notification](#image-push-notification) 기능이 추가되었습니다. 
- v2.4.02 
    - v2.3.2에서 패치된 Timeout 이벤트 처리가 적용되었습니다.
    - [Unity Plugin 2.2.01](https://github.com/adfresca/sdk-unity-sample/blob/master/README.md#release-notes) 버전을 지원합니다.
- v2.4.01
    - 앱 내에서 발생하는 In-App Purchase 데이터를 트랙킹할 수 있는 기능이 추가되었습니다. 자세한 내용은 [In-App Purchase Tracking](#in-app-purchase-tracking) 항목을 참고하여 주세요. [In-App Purchase Tracking](#in-app-purchase-tracking) 항목을 참고하여 주세요.
- **v2.3.4 _(2014/04/06 Updated)_**
   - 인-앱 메시징 캠페인을 통한 Reward Item 지급 기능을 지원합니다.
   - Incentivized CPA 캠페인 기능을 지원합니다. 자세한 내용은 [Cross Promotion Configuration](#cross-promotion-configuration) 항목을 참고하여 주세요.
   - AFRewardItemListener 구현 기능이 추가되어, 지급 가능한 아이템이 발생할 시에 자동으로 onReward 이벤트가 발생합니다. 보다 자세한 내용은 [Reward Item](#reward-item) 항목을 참고하여 주세요.
- v2.3.3 _(01/30/2014 Updated)_ 
    - Image Push Notifcaiton 기능이 추가되었습니다. 적용에 대한 자세한 내용은 [Image Notification](#image-notification) 항목을 참고하여 주세요.
    - showNotification() 메소드를 통해 표시되는 푸시 메시지에 [BigTextStyle](http://developer.android.com/reference/android/app/Notification.BigTextStyle.html)이 기본적으로 적용됩니다.
- v2.3.2 
    - load() 메소드를 호출한 후 지정된 요청 시간이 초과된 경우 (Timeout), AFShowListener 리스너의 onFinish() 이벤트가 발생하도록 수정되었습니다. onFinish() 이벤트 발생에 대한 설명은 [AFShowListener](#afshowlistener) 항목을 참고하여 주세요.
- v2.3.1
    - GCM Registration ID가 새로 등록되거나 변경 시, SDK가 ID값을 실시간으로 Nudge 서비스에 업데이트하도록 개선되었습니다. (기존에는 앱 실행 시에만 업데이트하였습니다.)
- v2.3.0 
    - Nudge SDK에서 Baidu Push를 이용할 수 있도록 지원합니다. 자세한 내용은 [Baidu Push Service](#baidu-push-service) 항목을 참고하여 주세요.
- v2.2.3
    - Push Messaging 캠페인에서 설정한 title, ticker 메시지가 표시될 수 있도록 지원합니다.
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
    - _Banner View_ 콘텐츠츠의 가로세로 비율을 유지하기 위한 `keep_aspect_ratio` attribute 가 `AFBannerView`에 추가되었습니다.
    - `AFRewardItem` 의 멤버변수들이 Deprecated 되었습니다. 각 멤버변수들의 Getter 를 사용해주시기 바랍니다. 멤버변수들은 private 제한자로 변경될 예정입니다.
- v2.1.0
    - `getAvailableRewardItems()`, `checkRewardItems()`, `checkRewardItems(boolean)` 메소드가 추가 되었습니다. _Incentivized Campaign_을 통해 유저에게 Reward Item 을 지급 할 수 있습니다.
- v2.0.0
    - `AdExceptionListner`, `AdException` 클래스가 Deprecated 되었습니다. `AFExceptionListner`, `AFException` 을 사용해주세요.
- v2.0.0.1
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
    - _Nudge_ 로고가 왼쪽으로 정렬 됩니다. 
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
    - _Nudge_ Android SDK가 출시 되었습니다. 기본적인 광고 호출 및 세션 로깅 기능을 지원 합니다.

