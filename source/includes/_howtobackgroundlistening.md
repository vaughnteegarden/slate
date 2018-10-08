## Background Listening

Background Listening enables a mode where your app listens for watermarked audio, and rather than displaying the linked item immediately, instead stores any items it detects (products or categories) in a list. When background listening is stopped, the list is presented to the consumer as a series of Touch Triggers for review and action. As the name "background listening" implies, listening can occur in the background, such as when the consumer is using another app, or when the phone is asleep. Background listening will pause if interrupted by a phone call, and then resume afterwards. Background listening requires that sound be detected through the phone's mic. Watermarked audio playing in another app while headphones are plugged in would not be detected.

The implementation for Android and IOS are somewhat different, so see individual sequence diagrams below. 

### IOS Background Listening

<img src="images/BackgroundListeningIOS.png" style="margin:6px 0;"><br/>[ <a href="images/BackgroundListeningIOS.png" target="_blank">View full size</a> ]

### Android Background Listening

<img src="images/BackgroundListeningAndroid.png" style="margin:6px 0;"><br/>[ <a href="images/BackgroundListeningAndroid.png" target="_blank">View full size</a> ]

#### 1. Start Background Listening, Listen, and Display Results

```swift
Module: ScanManager Background Listening
import UIKit
import RezolveSDK

class ScanManagerViewController: UIViewController {

    @IBOutlet var scanCameraView: ScanCameraView?

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        //self.mySession = ... // initialize session

        let scanManager = self.mySession?.getScanManager()

        scanManager?.productResultDelegate = self
        scanManager?.rezolveScanResultDelegate = self
        scanManager?.autoDetectManagerDelegate = self

        scanManager?.startVideoScan(scanCameraView: self.scanCameraView!)
        scanManager?.startAudioScan()
    }
}

extension ScanViewController: AutoDetectManagerDelegate
{
    func onAutoDetectStopListening(resolved: [AutoDetectResult]) {
        print( "Auto Detect Results:\n" )
        for current in resolved {
            print( current.description() )
        }
    }

    func onAutoDetectError(error: String) {
        print( "Auto Detect Error:\n" )
        print( error )
    }
}
```

```java
package com.rezolve.demo.content;

import java.util.List;
import android.support.v7.app.AppCompatActivity;
import com.rezolve.demo.utilities.Constants;
import com.rezolve.sdk.RezolveSDK;
import com.rezolve.sdk.core.interfaces.AutoDetectInterface;
import com.rezolve.sdk.core.managers.AutoDetectManager;
import com.rezolve.sdk.model.shop.Category;
import com.rezolve.sdk.model.shop.Product;

// To use Rezolve Auto Detect, the main Activity of the app must implement AutoDetectInterface
public class AutoDetectSampleActivity extends AppCompatActivity implements AutoDetectInterface {
    private AutoDetectManager rezolveAutoDetectManager;

    @Override
    public void onPause() {
        // When app goes background the app should start the service
        if (rezolveAutoDetectManager == null) {
            // Request RezolveSDK for a instance of the AutoDetectManager
            rezolveAutoDetectManager = RezolveSDK.getInstance(Constants.API_KEY, Constants.ENV).getRezolveSession().getAutoDetectManager();
        }
        // Start the service passing the caller Activity, an optional notification and the AutoDetectInterface Callback.
        // Here we don't pass the optional notification, so the RezolveSDK will provide a default Notification for the app.
        rezolveAutoDetectManager.startAutoDetectService(this, null, this);
        super.onPause();
    }

    @Override
    public void onResume() {
        super.onResume();
        // When the app is resumed we must stop the service, on stoping the service will return result to the callback method.
        if (rezolveAutoDetectManager != null) {
            rezolveAutoDetectManager.stopAutoDetectService(this);
        }
    }

    @Override
    public void onAutoDetectResults(List list) {
        // On the callback method the app gets a heterogeneous list that may contain several types of Objects
        int listSize = list.size();
        for (int i = 0; i <= listSize; i++) {
            Object item = list.get(i);
            try {
                // The item may be a Product
                if (item instanceof Product) {
                    Product product = (Product) item;
                    // Or it may be a Pair containing a Category and the merchantId who owns the Category
                } else if (item instanceof android.support.v4.util.Pair) {
                    Category category = (Category) ((android.support.v4.util.Pair) item).first;
                    String merchantId = (String) ((android.support.v4.util.Pair) item).second;

                    String categoryId = category.getId();
                    String categoryName = category.getName();
                    boolean categoryHasProducts = category.hasProducts();
                    boolean categoryHasSubCategories = category.hasCategories();
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

Use AutoDetectManagerDelegate (IOS) or AutoDetectManager (Android) to start listening for watermarked audio. When the user stops Background Listening, the results detected (if any) will be returned as an array. Iterate over this array to display detected items as Touch Triggers. 

Note: if you want to enable "back" navigation to the item list after the consumer views a list item, you should persist the list in your code. The SDK only returns the list when AutoDetect is stopped; it does not persist the list for you. 


#### 1a. Note that Android offers a Notification Helper

```java
package com.rezolve.demo.content;

import java.util.List;

import android.app.Notification;
import android.app.PendingIntent;
import android.content.Context;
import android.content.Intent;
import android.os.Build;
import android.support.v4.app.NotificationCompat;
import android.support.v7.app.AppCompatActivity;
import com.rezolve.demo.R;
import com.rezolve.demo.utilities.Constants;
import com.rezolve.sdk.RezolveSDK;
import com.rezolve.sdk.core.interfaces.AutoDetectInterface;
import com.rezolve.sdk.core.managers.AutoDetectManager;
import com.rezolve.sdk.model.shop.Category;
import com.rezolve.sdk.model.shop.Product;
import com.rezolve.sdk.notifications.NotificationActionReceivers;

// To use Rezolve Auto Detect, the main Activity of the app must implement AutoDetectInterface
public class AutoDetectSampleActivity2 extends AppCompatActivity implements AutoDetectInterface {
    private AutoDetectManager rezolveAutoDetectManager;

    @Override
    public void onPause() {
        // We may create a custom notification to be used with the service
        Notification optionalCustomNotification = createOptionalNotification(this);

        // When app goes background the app should start the service
        if (rezolveAutoDetectManager == null) {
            rezolveAutoDetectManager = RezolveSDK.getInstance(Constants.API_KEY, Constants.ENV).getRezolveSession().getAutoDetectManager();
        }
        // Start the service passing the caller Activity, an optional notification and the AutoDetectInterface Callback.
        // Here we send the custom notifiction we have created
        rezolveAutoDetectManager.startAutoDetectService(this, optionalCustomNotification, this);
        super.onPause();
    }

    @Override
    public void onResume() {
        super.onResume();
        // When the app is resumed we must stop the service, on stoping the service will return result to the callback method.
        if (rezolveAutoDetectManager != null) {
            rezolveAutoDetectManager.stopAutoDetectService(this);
        }
    }

    @Override
    public void onAutoDetectResults(List list) {
        // On the callback method the app gets a heterogeneous list that may contain several types of Objects
        int listSize = list.size();
        for (int i = 0; i <= listSize; i++) {
            Object item = list.get(i);
            try {
                // The item may be a Product
                if (item instanceof Product) {
                    Product product = (Product) item;
                    // Or it may be a Pair containing a Category and the merchantId who owns the Category
                } else if (item instanceof android.support.v4.util.Pair) {
                    Category category = (Category) ((android.support.v4.util.Pair) item).first;
                    String merchantId = (String) ((android.support.v4.util.Pair) item).second;

                    String categoryId = category.getId();
                    String categoryName = category.getName();
                    boolean categoryHasProducts = category.hasProducts();
                    boolean categoryHasSubCategories = category.hasCategories();
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

    private Notification createOptionalNotification(Context context) {
        // Here we create a regular notification to be presented by the service
        String CHANNEL_ID = "220518";
        int NOTIFICATION_ID = 230518;
        int NOTIFICATION_TURN_OFF_REQUEST_CODE = 230519;
        String ACTION_TURN_OFF = "action.turn.off";

        PendingIntent onNotificationTapIntent = null;
        Notification notification;
        Notification.Builder notificationBuilder;
        NotificationCompat.Builder notificationCompatBuilder;

        Intent turnOffIntent = new Intent();

        turnOffIntent.setAction(ACTION_TURN_OFF);
        turnOffIntent.setClass(this, NotificationActionReceivers.TurnOffBackgroundListenerReceiver.class);
        PendingIntent pendingTurnOffIntent = PendingIntent.getBroadcast(this, NOTIFICATION_TURN_OFF_REQUEST_CODE, turnOffIntent, PendingIntent.FLAG_CANCEL_CURRENT);

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            notificationBuilder = new Notification.Builder(this, CHANNEL_ID)
                    .setSmallIcon(R.drawable.default_notification_icon)
                    .setContentTitle(this.getString(R.string.notification_title))
                    .setContentText(this.getString(R.string.notification_text))
                    .setStyle(new Notification.BigTextStyle().bigText(this.getString(R.string.notification_big_text)))
                    .setAutoCancel(true)
                    .addAction(R.drawable.spinner, context.getString(R.string.notification_turn_off), pendingTurnOffIntent);

            if (onNotificationTapIntent != null) {
                notificationBuilder.setContentIntent(onNotificationTapIntent);
            }

            notificationBuilder.setOngoing(true);
            notification = notificationBuilder.build();
        } else {
            notificationCompatBuilder = new NotificationCompat.Builder(context, CHANNEL_ID)
                    .setSmallIcon(R.drawable.ic_slider_head)
                    .setContentTitle(context.getString(R.string.notification_title))
                    .setContentText(context.getString(R.string.notification_text))
                    .setStyle(new NotificationCompat.BigTextStyle().bigText(context.getString(R.string.notification_big_text)))
                    .setPriority(NotificationCompat.PRIORITY_DEFAULT)
                    .setAutoCancel(true)
                    .addAction(R.drawable.spinner, context.getString(R.string.notification_turn_off), pendingTurnOffIntent);

            if (onNotificationTapIntent != null) {
                notificationCompatBuilder.setContentIntent(onNotificationTapIntent);
            }

            notificationCompatBuilder.setOngoing(true);
            notification = notificationCompatBuilder.build();
        }
        return notification;
    }
}

```

This helper can be used to display notification to the user that Background Listening is active. 


#### 2. Display item Consumer selects using Trigger Manager

```swift
Module: TriggerManager
import UIKit
import RezolveSDK

class ScanManagerViewController: UIViewController {

    @IBOutlet var scanCameraView: ScanCameraView?

    var mySession: RezolveSession?

    override func viewDidLoad() {
        super.viewDidLoad()

        //self.mySession = ... // initialize session

        let url = URL(string: "http://rzlv.co/1/2/3/8?ad=20&placement=25")

        self.mySession.triggerManager.resolve(
            url: url!,
            productDelegate: self,
            onRezolveTriggerStart: {},
            onRezolveTriggerEnd: {},
            errorCallback: { error in }
        )
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()

        scanManager?.stop()
    }
}

extension ViewController: ProductDelegate {


    func onStartRecognizeImage() {

    }

    func onFinishRecognizeImage() {

    }

    func onProductResult(product: Product) {
    }

    func onCategoryResult(merchantId: String, category: RezolveCategory) {
		//display category result
    }

    func onCategoryProductsResult(
      merchantId: String,
      category: RezolveCategory, productsPage: PageResult<DisplayProduct>) {
		//display product result
    }

    func onError(error: String) {

    }
}
```

```java
rezolveSession.getTriggerManager().resolveTrigger("http://rzlv.co/2/3/13/169?ad=20&placement=25", new TriggerInterface() {
    @Override
    public void onProductResult(Product product) {  // display prouduct result
        Log.d("resolveTrigger", "onProductResult: "+product.getTitle());
    }
    @Override
    public void onCategoryResult(Category category, String merchantId) {  // display category result
        Log.d("resolveTrigger", "onCategoryResult: "+category.getName());
    }
    @Override
    public void onBadTrigger() { // called when provided url has bad syntax or is too short
        Log.d("resolveTrigger", "onBadTrigger");
    }
    @Override
    public void onError(HttpResponse httpResponse) {
        Log.d("resolveTrigger", "onError: "+httpResponse.getResponseJson());
    }
});

```

Use TriggerManager to display a selected Touch Trigger item. You will have to pass a valid product url into TriggerManager, in the form:

`http://rzlv.co/[partnerId]/[merchantId]/[categoryId]/[productId]?ad=[adId]&placement=[placementId]`

Get `partnerId`, `merchantId`, `categoryId`, `productId`, `adId` and `placmentId` from the returned list array for that item. If no `adId` and/or `placementId` are available, omit the `?ad=[adId]&placement=[placementId]` part of the url (note that in this case, no stats will be gathered when this item is displayed or purchased).