## Contents
- [Basic Integration](#basic-integration)
  - [Installation](#installation)
  - [Start Session](#start-session)
  - [In-App Messaging](#in-app-messaging)
  - [Push Messaging](#push-messaging)
  - [Test Device Registration](#test-device-registration)
  - [Test Mode](#test-mode)
- [IAP, Reward and Sales Promotion](#iap-reward-and-sales-promotion)
  - [In-App Purchase Tracking](#in-app-purchase-tracking)
  - [Give Reward](#give-reward)
  - [Sales Promotion](#sales-promotion)
- [Dynamic Targeting](#dynamic-targeting)
  - [Custom Parameter](#custom-parameter)
  - [Stickiness Custom Parameter](#stickiness-custom-parameter)
  - [Marketing Moment](#marketing-moment)
- [Advanced](#advanced)
  - [Custom Banner (Android Only)](#custom-banner)
  - [AFShowListener](#afshowlistener)
  - [Timeout Interval](#timeout-interval)
- [Reference](#reference)
  - [Deep Link](#deep-link)
  - [Cross Promotion Configuration](#cross-promotion-configuration)
  - [Google Referrer Tracking](#google-referrer-tracking)
  - [Proguard Configuration](#proguard-configuration)
- [Troubleshooting](#troubleshooting)
- [Release Notes](#release-notes)

* * *

## Basic Integration

### Installation

Download our SDK with the following link:

[Android SDK Download](http://file.adfresca.com/distribution/sdk-for-Android.zip) 

To add our SDK into your android project, please follow the instructions below:

1) Copy **AdFresca.jar** and **adfresca_attr.xml** to **lib** and **res/values** respectively.

<img src="https://adfresca.zendesk.com/attachments/token/bja88u9zake4knm/?name=add_adfresca_jar_and_attr_xml.png" width="300"/>

2) Update build path of your project.
- Right-click on your project and click **Properties**.
- Select **Java Build Path** and **Libraries** tab.
- Click **Add JARs** and select **lib/AdFresca.jar**.

<img src="https://adfresca.zendesk.com/attachments/token/ogcnzf3kmyzbcvg/?name=add_jar.png" width="600" />

3) Modify **AndroidManifest.xml**
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

Now, let’s start to put some simple SDK codes in your app. You first need to call startSession() method with setting your API Key. To get your API Key, go to our [Dashboard](https://dashboard.nudge.do) and then click 'Settings - API Keys' button in your app's 'Overview' page.

Put startSession() in your first activity class. Please make sure that this method is called once while your app is running.

```java
protected void onCreate(Bundle savedInstanceState) {
  ....
  AdFresca.setApiKey(API_KEY);
  AdFresca.getInstance(this).startSession();
}
```

### In-App Messaging

With the in-app messaging feature, you can deliver a message to your in-app users in real time. Simply put 'load()' and 'show()' methods where you want to deliver a message. The type of message can be an interstitial image, text, and iframe webpage. The message is only shown when your user matches the in-app messaging campaign's target logics. We will discuss in more detail about the in-app messaging's dynamic targeting features in the [Dynamic Targeting](#dynamic-targeting) section.

```java
protected void onCreate(Bundle savedInstanceState) {
  ...
  AdFresca fresca = AdFresca.getInstance(this);
  fresca.load();
  fresca.show();
}
```

When you first call in-app messaging methods, you will see the test message below. If you tap on the image, it will redirect to the product page of the app on the app store. You will hide this test message by changing the test campaign mode configuration later.

<img src="https://adfresca.zendesk.com/attachments/token/zngvftbmcccyajk/?name=device-2013-03-18-133517.png" width="240" />
&nbsp;
<img src="https://adfresca.zendesk.com/attachments/token/phn4fcpvbi2damx/?name=device-2013-03-18-133443.png" height="240" />
* * *

### Push Messaging

You can also deliver your push messages anytime you want. Follow the steps below to configure the push notification settings in your app.

Before you start, you need to have your GCM project number from Google API Console, and set GCM API Key value to our [Dashboard](https://dashboard.nudge.do). Please refer to '[Android Push Notification Guide](https://adfresca.zendesk.com/entries/82771087)'

1) Check your GCM library installation
  - To use GCM service, your should have GCM client library in your project.
  - If you don't have GCM client library yet, get [GCM Helper Library from Google](http://code.google.com/p/gcm/source/browse/), and then add **/gcm-client/dist/gcm.jar** file into your project.
    
2) Add some codes to AndroidManifest.xml

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

- If you already have your own GCMReceiver and GCMIntentService classes, you only need to put some SDK codes in your own GCMIntentService class.
- If you haven't implemented any GCM classes yet, please refer to the sample codes of [GCMReceiver](https://gist.github.com/sunku/29906033dcee764ef022) and [GCMIntentService](https://gist.github.com/sunku/05c5e4feb3d0fb8d4088)

3) Set GCM Registration ID

```java
AdFresca fresca = AdFresca.getInstance(this);
fresca.setPushRegistrationIdentifier("GCM_REGISTRATION_ID_OF_THIS_DEVICE");
```
- If you don't have a GCM registration id yet, please refer to '[How to Get GCM Registration ID](https://gist.github.com/sunku/b47eecee77afe40aa515)'

4) Implement GCMIntentService

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

- If the push message has a Deep Link, our SDK will execute the url when users touch the message.
- If the push message does not have a Deep Link, our SDK will execute your targetActivityClass instead.
- You may also able to use notification.setSound(uri) method to play a sound when the message arrives.
* * *

### Test Device Registration

Nudge supports a test campaign feature. With the test campaign feature, you can deliver test messages to only registered test devices. 

To register your test device to our dashboard, you need to know your test device ID from our SDK. Our SDK provides two ways to show test device IDs.
 
1. Using getTestDeviceId() method
  - After connecting your device with ADB, you can simply print out test device ID with a logger.

  ```java
  AdFresca fresca = AdFresca.getInstance(this);
  Log.d(TAG, "Nudge Test Device ID is = " + fresca.getTestDeviceId());
  ```

2. Displaying test device ID on your app screen using printTestDeviceId property
  - When you are not able to connect tester's device in your office, you have to set printTestDeviceId to true, and then let them install this app build. They can see their own test device ID on the app screen. 
  - It is useful when testers are working remotely. 
  - printTestDeviceId property must be set to false when you distribute your app on the store. 

  ```java
  AdFresca fresca = AdFresca.getInstance(this);
  fresca.setPrintTestDeviceId(true);
  fresca.load();
  fresca.show();
  ```

After you have your test device ID, you have to register it to [Dashboard](https://dashboard.nudge.do). You can register your device in the 'Test Device' menu.

### Test Mode

Nudge SDK supports a test mode feature. With the test mode feature, you can verify your SDK codes. When you add **setTestMode(true)** code, SDK will print a log message with a result for each your SDK code. 

  ```java
 AdFresca.setTestMode(true);
  ```

<img src="https://s3-ap-northeast-1.amazonaws.com/file.adfresca.com/guide/sdk/android_sdk_test_mode.png" width="900" />

The test mode currently provides 'Start Session', 'Push Messaging', 'In-App Purchase Tracking', 'Custom Parameter', and 'Stickiness Custom Parameter' logs. Other feature support will be available soon.

* * *

## IAP, Reward and Sales Promotion

### In-App Purchase Tracking

With In-App-Purchase Tracking, you can analyze all the purchases of your users, and use it for targeting specific user segments to display your campaigns. 

There are two types of purchases you can track with our SDK.

1. **Hard Currency Item Purchase Tracking:**  The purchases made with real money. For example, user purchased ’$1.99' to get 'Gold 100' cash item.
2. **Soft Currency Item Purchase Tracking:** The purchases made with virtual money. For example, user purchased 'Gold 10' to get 'Rocket Launcher' item 

You don't need to write down any item list manually. All the Items tracked by our SDK are automatically added to our dashboard. To see the list of items, go to 'Overview > Settings > In-App Items' page on our dashboard.

Let's get started and implement SDK codes with the examples below. 

#### Hard Currency Item Tracking

The purchase of 'Hard Currency Items’ is made with the store's billing library such as Google Play Billing. When your user purchased the item successfully, simply create AFPurchase object and use logPurchase() method. Also, call CancelPromotionPurchase() method when a user cancelled or failed to purchase.

Example: Google Play Billing 
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
      String currencyCode = "USD"; // The currencyCode must be specified in the ISO 4217 standard. (ex: USD, KRW, JPY). For Google Play, use the currency code of 'default price' in your account.
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

The above example is written for Google Play. You can also get the required values from other billing libraries such as Amazon.

For more details of AFPurchase object with the hard currency item, check the table below.

Method | Description
------------ | ------------- | ------------
setItemId(string) | Set the unique identifier of your item. This value may not be different per the os platform or app store. We recommend that you make this value unique for all platforms and stores. Our service can distinguish each item by this value.
setCurrencyCode(string) | Set the current code of IOS 4217 standard. For Google Play, use the currency code of 'default price' in your account. For Amazon, set 'USD' value since Amazon only supports USD.
setPrice(double) | Set the item price. you may use SkuDetails's value or manually set the value from your server.
setPurchaseDate(date) | Set the date of purchase. You may use purchase.getPurchaseTime() value. If you set null value, it will be automatically recorded by our SDK and server. Please don't use local time of the user's device.
setReceipt(string, string, string) | Set the receipt property of purchase object (Google Play only). We will use it to verify the receipt in the future. 

#### Soft Currency Item Tracking

When users purchase soft currency items in the app, you can also create AFPurchase object and call logPurchase() method. Also, call cancelPromotionPurchase() method when a user cancelled or failed to purchase. 

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

public void onPurchaseSoftItemFailure() {
  AdFresca.getInstance(this).cancelPromotionPurchase();
}
```

For more details of AFPurchase object with the soft currency item, check the table below.

Method | Description
------------ | ------------- | ------------
setItemId(string) | Set the unique identifier of your item. This value may not be different per the os platform or app store. We recommend that you make this value unique for all platforms and stores. Our service can distinguish each item by this value.
setCurrencyCode(string) | Set the item's soft currency code. (ex: 'gold', 'gas')
setPrice(double) | Set the item price. You may get this value from your server. (ex: 100 of gold)
setPurchaseDate(date) | Set the date of purchase. If you set null value, it will be automatically recorded by our SDK and server. Please don't use local time of the user's device.

#### IAP Troubleshooting

After you call logPurchase() method, the purchase data is updated to our dashboard in real-time. You can see the list of updated item in 'Overview > Settings > In-App Items' menu.

If you can't see any data in our dashboard, your AFPurchase object may be invalid. To check it, you can implement  AFPurchaseExceptionListener and call log logPurchase(purchase, listener) method. 

```java
AdFresca.getInstance(this).logPurchase(purchase, new AFPurchaseExceptionListener(){
  public void onException(AFPurchase purchase, AFException e) {
    Log.e(TAG, (purchase == null ? "purchase=null" : purchase.toString()));
    Log.e(TAG, e.getMessage());
  }
});
```

* * *

### Give Reward

When you set the 'Reward Item' section of the reward campaign or 'Inventive item' section of the incentivized CPI & CPA campaign, you should implement this 'reward item' code to give a reward item to your users.

When implementing reward item codes, you can check if your user has any reward to receive, and then a notice will be sent with reward item info.

To implement codes, we use two codes below:

- checkRewardItems method: This method is to check if any item is available to receive. We recommend to put this code when the app becomes active. 
- AFRewardItemListener implementation: When the reward condition is completed with the current user, onReward event is automatically called with AFRewardItem object from our SDK. You can give an item to the user with AFRewardItem object.

```java
public void onResume() {
  ...

  AdFresca.setRewardItemListener(new AFRewardItemListener(){
      @Override
      public void onReward(AFRewardItem item) {
        String logMessage = String.format("You got the reward item! (%s)", item.getName());
        Log.d(TAG, logMessage);
        
        // Now, you can give an item to users.  
        sendItemToUser(currentUserId, item.getUniqueValue(), item.getQuantity(), item.getSecurityToken());		
      }});
          
  AdFresca fresca = AdFresca.getInstance(this);
  fresca.checkRewardItems();
}
```

onReward event is called when each type of campaign's reward condition is completed.

- Reward Campaign: The event is called when your user sees the campaign contents.
- Incentivized CPI Campaign: The event is called when our SDK checks Advertising App's install.
- Incentivized CPA Campaign: The event is called after our SDK checks Advertising App's install and the user called the targeted marketing event in Advertising App.
 
If your users have any network connectivity issues, our SDK stores the reward data in the app's local storage, and then re-checks at the next app session. So, we guarantee every user will always get the reward they are entitled to.


#### implementing sendItemToUser()

You should give a reward item to your user using your own client code or back-end server api. Your client may send an api request with an unique value of item, quantity and security token values to your server. Then the server application will add a reward item to the user's item inventory.

#### Hack Proof

Our SDK never calls itemRewarded event more than once per campaign. We always check with device identifiers to avoid abuse. However, It is still possible for hackers to hijack your api request between your client and back-end server. To prevent this issue, we provide a security token value. A security token is an unique value per campaign. You can generate the token while you're creating a reward campaign. You can provide hack-proof rewards using the security token as follows:

1. You will store a security token to your own database before starting a reward campaign. You should reject any reward requests with an invalid token value.
2. If some users are trying to request with the same token value more than once, you should reject those requests.
3. If you think your security token is exposed to hackers, you can always change the value in our dashboard.

### Sales Promotion

Using sales promotion campaigns, you can promote your in-app item to your users. When users tap on an action button of an image message, a purchase UI will appear to proceed with the user's purchase. Our SDK will automatically detect if the users made a purchase or not, and then will update the campaign performance to our dashboard in real time.

To apply our promotion features, you should implement AFPromotionListener. onPromotion() event is automatically called when users tap on an action button of an image message for sale promotion campaigns. You just need to show the purchase UI of the promotion item using 'promotionPurchase' object. 

For Hard Currency Items, you should use your in-app billing library codes to show the purchase UI. You can get the SKU value from getItemId() method of promotionPurchase object.

For Soft Currency Items, you should use your own purchase UI which might be already implemented in your store page. Also there are discount options for soft currency item sales promotion campaigns. You can check the discount type using getDiscountType() method of promotionPurchase object.

1. **Discount Price**: Users can buy a promotion item at a specific discounted price. You can get price from getPrice() method.

2. **Discount Rate**: users can buy a promotion item at a discounted rate. You will calculate the discounted price by applying the discount rate which can be earned from getDiscountRate() method.

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

Our SDK will detect if users made a purchase using our [In-App Purchase Tracking](#in-app-purchase-tracking) features. Therefore, you should implement it to complete this promotion feature. Please make sure that you implement 'cancelPromotionPurchase()' method when the users cancelled or failed to purchase items.
* * *

## Dynamic Targeting

### Custom Parameter

Our SDK can collect user specific profiles such as level, stage, maximum score, etc. We use it to deliver a personalized and targeted message in real time to specific user segments that you can define.

To implement codes, simply call setCustomParameterValue method with passing parameter's unique key and its value.

You will call the method after your app is launched and the values have changed. (if you can't set the values without user sign in, you may set them right after users sign in.)

```java
  public void onCreate() {
    AdFresca fresca = AdFresca.getInstance(this);     
    fresca.setCustomParameterValue("level", User.level);
    fresca.setCustomParameterValue("age", User.age);
    fresca.setCustomParameterValue("facebook_flag", User.hasFacebookAccount);
    fresca.startSession();
  }
  
  .....
  
  public void onUserLevelChanged(int level) {
    User.level = level
    
    AdFresca fresca = AdFresca.getInstance(this);     
    fresca.setCustomParameterValue("level", User.level);
  }
```

After you write the codes, you will be able to see a list of custom parameters you added on [Dashboard](https://dashboard.nudge.do). 1) Select a App 2) In 'Overview' menu, click 'Settings - Custom Parameters' button.

<img src="https://s3-ap-northeast-1.amazonaws.com/file.adfresca.com/guide/sdk/custom_parameter_index.png">

You need to set 'Name' value of each custom parameter to activate. You can activate custom parameters up to 20. Nudge only allows activated custom parameters to collect data and provide targeting features.

* * *

### Stickiness Custom Parameter

(Stickiness Custom Parameter is currently in beta. To use this feature, contact our [support team](mailto:support@nudge.do))

If your app has any value to measure user stickiness such as ‘play count’ in a stage based game, you can use it to create a  'Stickiness Custom Parameter' with Nudge. ou can define user segments such as 'users who played 30 times in a week' and 'Users who played 5 times today'.

To begin, you first need to set a new custom parameter such as 'play count’, and then configure it to a stickiness mode (stickiness mode can only be configured by Nudge team currently).

To implement codes, simply pass the value to **incrCustomParameterValue(key, amount)** method whenever the stickiness value is increased. Our SDK will automatically calculate the accumulated value and daily increased value and update user profiles.

After you write the code, you can now use 'Today's play count, 'Average play count in a week', and 'Total play count in a week' conditions to define your user segments in our dashboard.

```java
public void OnGameFinished {
  AdFresca fresca = AdFresca.getInstance(this);     
  fresca.incrCustomParameterValue("play_count", 1);
}
```

If your app was already launched to app stores, you need to set the accumulated value before you call incrCustomParameterValue method. You can check if the custom parameter value is already set or not by using **hasCustomParameterValue(key)** method. If the value is not set yet, set the accumulated value from your app server.

```java
public void onUserSignIn {
  ....

  AdFresca fresca = AdFresca.getInstance(this);     
  if (!fresca.hasCustomParameterValue("play_count")) {
    fresca.setCustomParameterValue("play_count", User.totalPlayCount);
  }
}
```

* * *

### Marketing Moment

A Marketing Moment means the exact moment you want to engage with your users. For example, you may need to deliver a message when the user completes a quest or enters an item store. You will be able to use it with the [custom parameters](#custom-parameter) so you can deliver a personalized and targeted message at a specific moment in real time.

To implement codes, simply call **load(index)** method with passing marketing moment's index. You can get the marketing moment's index in our [Dashboard](https://dashboard.nudge.do): 1) Select a App 2) In 'Overview' menu, click 'Settings - Marketing Moment' button. 

You will call the method after the moment has happened in the app.

```java
  public void OnUserDidEnterItemStore() {
    AdFresca fresca = AdFresca.getInstance(this);
    fresca.load(EVENT_INDEX_STORE_PAGE); 
    fresca.show();
  }

  public void onUserLevelChanged(int level) {
    User.level = level
    
    AdFresca fresca = AdFresca.getInstance(this);     
    fresca.setCustomParameterValue(CUSTOM_PARAM_INDEX_LEVEL, User.level);
    fresca.load(MOMENT_INDEX_LEVEL_UP); 
    fresca.show();
  }
```

## Advanced

### Custom Banner

_Android SDK_ provides with two kinds of _Custom Banner_ that makes it possible to show images of various sizes. One is [Floating View](#floating-view) and the other is [Banner View](#banner-view).

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

#### Floating View

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

#### Banner View

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
  AdFresca fresca = AdFresca.getInstance(this);
  fresca.startSession();
  fresca.load(EVENT_INDEX_MAIN_PAGE_FOR_BANNER); // load the content for Banner View of Main page
  fresca.load(EVENT_INDEX_MAIN_PAGE_FOR_INTERSTITIAL); // load the content for Interstitial View of Main page
  fresca.show(); // show all loaded contents.
}
```

* * *

### AFShowListener

_AFShowListener_ is called when a campaign is finished.

When a campaign is finished, it means the following two cases.

1. The content was shown and it has been closed.
2. A campaign was failed because it expired or there was no [Custom Banner](#custom-banner) for its _Image Size Index_.

You can differentiate these 2 cases by `view` that is given by `AFShowListener.show(int eventIndex, AFView view)`

- if `view != null`, it was the first case. At this time, `view` is one of [ _Default View_ | _Floating View_ | _Banner View_ ]. (`view` is _Default View_ if `view.isDefaultView()` returns true.)
- if `view == null`, it was the second case.

```java
AdFresca fresca = AdFresca.getInstance(this);
fresca.startSession();
fresca.load(EVENT_INDEX_STAGE_CLEAR);
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
**Example:** The following code shows content at _Intro Activity_ and will move to _Main Activity_ when it is finished.

```java
AdFresca fresca = AdFresca.getInstance(this);
fresca.startSession();
fresca.load(EVENT_INDEX_INTRO);
fresca.show(EVENT_INDEX_INTRO, new AFShowListener(){
  @Override
  public void onFinish(int eventIndex, AFView view) {
    startActivity(new Intent(IntroActivity.this, MainActivity.class));
  }
});
```

**_Caution!_**
If a user clicked contents that opens Google Play or other applications, the user will leave your app screen.

In this case, if you implemented onFinish() event like above example, the user may see an unnatural paging animation since the app was temporally paused by another application.

To fix this issue, follow the steps below:

In the admin dashboard, you should change 'Close mode' to 'Override' in your event settings. (it will prevent to close view when user clicked)
In app codes, override Activity's onResume() method like below:

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

### Timeout Interval

You can set a timeout interval for a messaging request. If message is not loaded within this time interval, Message won't be displayed to users and SDK will return the control to your app.

Default is 5 seconds and you can set from 1 seconds to 5 seconds.

```java
  AdFresca fresca = AdFresca.getInstance(this);
  AdFresca.setTimeoutInterval(5) // # 5 seconds
  fresca.load();
  fresca.show();
```

## Reference

### Deep Link

You can set your own URL Schema as a 'Deep Link' in the campaigns and you can navigate your users to the specific page or do some custom actions when the user clicks the image message. 
To use this feature, add scheme in AndroidManifest.xml

```xml
  <activity android:name=".MainActivity">
      <intent-filter> 
             <action android:name="android.intent.action.VIEW" /> 
             <category android:name="android.intent.category.DEFAULT" /> 
             <category android:name="android.intent.category.BROWSABLE" /> 
             <data android:scheme="myapp" android:host="myaction" />
        </intent-filter> 
  </activity>
```

In this example, you can set url as myapp://myaction?name=abc to launch MainActivity when user responds.

on MainActivity, you can get parameter values (name=abc) as example codes below.

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

#### Handling In-App Deep Link with Coscos2d-x

Unlike the native android application that uses multiple activities as its pages, coscos2d-x and Unity engines use only one activity and implements the engine's own paginations internally. So, you should not run any new activity when deep link is executed by our SDK.

You need to override startActivity(intent) of Cocos2dxActivity to handle deep links.

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

* * *

### Cross Promotion Configuration

Using Incentivized CPI & CPA Campaigns, your users in 'Media App' can get an incentive item when they install 'Adverting App' from the campaigns.

- Medial App: The media app which displays the promotion image and gives an incentive item to users
- AdvertisingApp: The promotion app which is displayed with an image in the media app's screen.

For more details of Incentivized campaigns and configuration guide in dashboard, please refer to 'Understanding Cross-promotion (Korean)'  guide.

To integrate our SDK with this feature, you should set URL Schema value for the adverting app and implement codes to give an incentive item to users in the media app.

#### Advertising App Configuration:

  For Android, SDK uses the package name to check if the advertising app is installed in the same device. 

  You can find the package name in AndroidManifest.xml

  ```xml
  <manifest package="com.adfresca.demo">
    ...
  </manifest>
  ```

  In this case, you should set the CPI Identifier value of advertising app to "com.adfresca.demo" in our dashboard.

  For Incentivized CPI Campaigns, our SDK Installation of the advertising app is not required. You only check the package name.

  However, if you use Incentivized CPA Campaigns, our SDK installation is required and you should also implement our 'Marketing Moment' feature to check for a reward condition. For example, when you set the reward condition to check 'Tutorial Complete' event, you should call the marketing moment method to inform that your user achieved the goal.
    
  ```java
  public void onUserFinishTutorial() {
    AdFresca fresca = AdFresca.getInstance(this);     
    fresca.load(MOMENT_INDEX_TUTORIAL); 
    fresca.show();
  }
  ```

#### Media App Configuration:

  To give an incentive item to the media app's users, please refer to the [Give Reward](#give-reward) section.

* * *

### Google Referrer Tracking

You can see how many users installed your app through Google Play Campaign by tracing the referrer information.

To fetch referrer and send it to our SDK, you can add a receiver and do test below.

1) Check the receiver of AndroidManefest.xml

By registering this receiver, our SDK will automatically collect referrer information and update to the Nudge server. 

```xml
<receiver android:name="com.adfresca.sdk.referer.AFRefererReciever" android:exported="true">
  <intent-filter>
          <action android:name="com.android.vending.INSTALL_REFERRER" />
      </intent-filter>
</receiver>
```

2) Test referrer using ADB

After connecting the Android device to your computer, open adb shell (adb shell is located platform-tools directory inside of installed Android SDK directory).

Then you can manually send INSTALL_REFERRER message to your device. Just change package name and referrer values below.
(You can see detailed information of each referrer value in [Google Play - Campaign Parameters](https://developers.google.com/analytics/devguides/collection/android/v2/campaigns#campaign-params))

```sh
am broadcast -a com.android.vending.INSTALL_REFERRER -n YOUR_PACKAGE/com.adfresca.sdk.referer.AFRefererReciever --es "referrer" "utm_source=test_source&utm_medium=test_medium&utm_term=test_term&utm_content=test_content&utm_campaign=test_name"
```
3) Check if referrer was successfully set on our SDK

```java
  AdFresca adfresca = AdFresca.getInstance(this);
  Log.v(TAG, "Google Referrer = " + adfresca.getReferrer());
``` 
(Advanced) If you already use another broadcast receiver to handle INSTALL_REFERRER, you can manually set your referrer by calling setReferrer(string) method

Caution: After your device updates referrer once to our service, it won't be able to change the referrer values for this device.

* * *

### Proguard Configuration

If you use Proguard to protect your APK, you should add exception configurations for our Android SDK. Add following lines of codes to ignore our SDK, OpenUDID, and Google Gson. 

```java
-keep class com.adfresca.** {*;} 
-keep class com.google.gson.** {*;} 
-keep class org.openudid.** {*;} 
-keep class sun.misc.Unsafe { *; }
-keepattributes Signature 
```

## Troubleshooting

if our SDK can't show any message or raise errors, you can debug by implementing AFExceptionListener.

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

- **v2.4.9 _(2015/03/27 Updated)_**
  - [Test Mode](#test-mode) is added.
- v2.4.8
  - for [Image Push Notification](#image-push-notification), SDK can download an image uploaded in dashboard.
- v2.4.7
  - [Custom Parameter](#custom-parameter) provides 'string' unique key. (Integer key is still available)
- v2.4.6
  - Add hasCustomParameterWithIndex method. 
- v2.4.5
  - HARD_ITEM and SOFT_ITEM enums are added to AFPurchase class to replace ACTUAL_ITEM and VIRTUAL_ITEM which will be deprecated. Please refer to [In-App Purchase Tracking](#in-app-purchase-tracking) section.
- v2.4.4
  - Support A/B Test feature. No SDK cod is required.
- v2.4.3 
  - Support Stickiness Custom Parameter BETA.
- v2.4.2 
  - Support soft currency item sales promotion campaign with discount options. Please refer to [Sales Promotion](#sales-promotion) section.
  - SDK will match multiple campaigns and show multiple messages in one marketing moment request.
- v2.4.1
  - Support hard currency item sales promotion campaign. Please refer to [Sales Promotion](#sales-promotion) section.
  - Support security token of reward campaign's hack proof. Please refer to [Give Reward](#give-reward) section.
  - Add cancelPromotionPurchase() method to [In-App Purchase Tracking](#in-app-purchase-tracking)
  - Support tap area feature.
  - Include IAP Beta features to 2.4.1
- v2.4.04
  - Include v2.3.4
- v2.4.03
  - Include v2.3.3
- v2.4.02
  - Include v2.3.2
  - Unity Plugin 2.2.01 is supported.
- v2.4.01
  - [In-App Purchase Tracking](#in-app-purchase-tracking) is added.
- v2.3.4
  - Giving Reward Item from in-app messaging campaign is supported.
  - Incentivized CPA Campaign is supported with cross promotion feature.
  - AFRewardItemListener is added. For details, please refer to [Give Reward](#give-reward) section.
- v2.3.3
  - Image Push Notifcaiton feature is added.
  - showNotification uses BigTextStyle as default.
- v2.3.2 
  - When load() method method is called but timed out, AFShowListener's onFinish() event will be called by SDK. Please refer to [AFShowListener](#afshowlistener) section for detailed information of onFinish() event
- v2.3.1 
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
- v2.0.0.1
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
    - 'Nudge' logo is now located on the left of top bar.
- v0.9.5
    - AD Slot feature added as an In-App Messaging feature added (See 'AD Slot Setting')
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
    - Nudge iOS SDK is now released! basic AD feature is included.
