## Location Triggers

Location Triggers are a powerful SDK feature introduced in 2020 that allows the developer to detect when the consumer has entered a predefined geozone and pop a notification of a product, informational page, or Act. A geozone is defined as a radius of X meters around a specified lat/long point, as set up by a merchant or partner in the Rezolve portal.

When the app is active, the SDK continuously samples the consumer's location, and when the consumer is within the radius of one or more geozones, events fire to alert the system to perform the desired behavior. The SDK intelligently handles areas with a high density of overlapping geozones to prevent spamming the user with notifications. If the consumer is in an area with multiple geozones, the SDK bundles multiple notifications into one alert that contains the messages from all the relevant zones.

**Topics**

* <a href="#setup-location-detection">Setup location detection</a>
* <a href="#initializing-location-detection">Initializing location detection</a>
* <a href="#detecting-geozones">Detecting geozones</a>
* <a href="#handling-detection-and-notification">Handling detection and notification</a>

### Setup location detection

```swift
// Add the following permission descriptions on your Info.plist.
- NSLocationAlwaysUsageDescription
- NSLocationAlwaysAndWhenInUseUsageDescription
- NSLocationWhenInUseUsageDescription

// Enable Background Modes for your app’s target.
- Location Updates
- Background Fetch
```
```java
// Taken from App.java in the Sample App project  

// Set up a Notification Helper

// Notes:
// Geofence and Location Detection service share the same notification
// See geofenceForegroundNotificationProperties to define the look of the notification
// Foreground notification is required for the foreground service. 
// This prevents the app from getting killed by the system. 

NotificationHelper notificationHelper = new NotificationHelperImpl(this);
Notification notification = notificationHelper.createNotification(
    1,
    getString(R.string.app_name),
    null,
    null,
    null,
    geofenceForegroundNotificationProperties
 );

```

This section describes how to set up your project to use the location features of the Rezolve SDK.  

### Initializing location detection

```swift
// On Rezolve class initialization you need to pass your SSP configuration into sspActManagerSettings parameter, which is a class of RezolveSDK.SspActManagerSettings. As an example, it has the following structure.

let sspActManagerSettings = SspActManagerSettings(
    auth0ClientId: "{String}",
    auth0Secret: "{String}",
    auth0Audience: "{String}",
    auth0Endpoint: "{String}",
    sspEndpoint: "{String}",
    sspWidth: "{String}",
    baiduLocationKey: "{String}"
)
// Adding a baiduLocationKey is not mandatory, but will definitely increase tracking accuraccy if you are targeting regions located in China mainland.
```
```java
// Taken from App.java in the Sample App project 

// Create and start Location Provider
// Make sure you have secured the location permission before starting the location provider.
final LocationProviderFused locationProviderFused = LocationProviderFused.create(this, notification);
locationProviderFused.start();

// Next, set up Geofence detection and Resolvers 

// Set up SspActManager 
SspActManager sspActManager = new SspActManager(httpClient);

// Set up Rezolve Configuration Builder (this also supports the image/audio Scanner function)
new ResolverConfiguration.Builder(rezolveSDK)
    .enableBarcode1dResolver(true)
    .enableCoreResolver(true)
    .enableSspResolver(sspActManager, 400)
    .build(this);

// Set up GeofenceMananger
final GeofenceManager geofenceManager = new GeofenceManager.Builder()
    .sspActManager(sspActManager)
    .engagementsUpdatePolicy(new EngagementsUpdatePolicy.Builder().build())
    .notificationChannelPropertiesList(geofenceLocationChannels)
    .engagementAlertNotification(geofenceAlertNotificationProperties)
    .context(this)
    .build();
```

Initializing location detection requires setting up the `SspActManager` with appropriate settings.



### Detecting geozones

```swift
// Setting up Rezolve SDK with an active SSP Act Manager configuration.
let sdk = Rezolve(apiKey: Config.rezolveApiKey,
    partnerId: Config.partnerId,
    subPartnerId: nil,
    environment: Config.env,
    config: nil,
    sspActManagerSettings: sspActManagerSettings,
    coordinatesConverter: CoordinatesConverter.default)

// Start monitoring for Nearby Engagements.
let ssp = RezolveService.sdk?.createRezolveSsp()
ssp?.nearbyEngagementsManager.startMonitoringForNearbyEngagements()
ssp?.nearbyEngagementsManager.debugNotifications = false
ssp?.nearbyEngagementsManager.delegate = self

// Stop monitoring for Nearby Engagements.
ssp?.nearbyEngagementsManager.stopMonitoringForNearbyEngagements()
ssp?.nearbyEngagementsManager.stopUpdatingDistanceFromNearbyEngagements()
ssp?.nearbyEngagementsManager.resetNotificationSuppressData()
ssp?.nearbyEngagementsManager.delegate = nil
```
```java
// Taken from App.java in the Sample App project 

// Make sure the location provider is started before detection.
// Based on the provided EngagementsUpdatePolicy  the GeofenceManager will manage geofence updates

final GeofenceManager geofenceManager = new GeofenceManager.Builder()
                .sspActManager(sspActManager)
                .engagementsUpdatePolicy(new EngagementsUpdatePolicy.Builder().build())
                .notificationChannelPropertiesList(geofenceLocationChannels)
                .engagementAlertNotification(geofenceAlertNotificationProperties)
                .context(this)
                .build();
registerGeofenceListener();
locationProviderFused.start();
geofenceManager.startGeofenceTracking();
private void registerGeofenceListener() {
        IntentFilter intentFilter = new IntentFilter();
        intentFilter.addAction(ACTION_GEOFENCE_NOTIFICATION_DISPLAYED);
        intentFilter.addAction(ACTION_GEOFENCE_NOTIFICATION_SELECTED);
        registerReceiver(geofenceBroadcastReceiver, intentFilter);
    }
    BroadcastReceiver geofenceBroadcastReceiver = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            final String action = intent.getAction();
            final String sender = intent.getStringExtra(KEY_SENDER_PACKAGE_NAME);
            if(!context.getPackageName().equalsIgnoreCase(sender)) {
                Log.d(TAG, "Ignoring intent from: " + sender +", expected: " + context.getPackageName());
                return;
            }
            if(action != null) {
                switch (action) {
                    case ACTION_GEOFENCE_NOTIFICATION_DISPLAYED: {
                        final String name = intent.getStringExtra(KEY_NAME);
                        final String shortDescription = intent.getStringExtra(KEY_DESCRIPTION_SHORT);
                        final String actId = intent.getStringExtra(KEY_ACT_ID);
                        final SspObject sspObject = getSspObjectFromIntent(intent);
                        Log.d(TAG, action + ": " + name + ", " + shortDescription + ", " + actId + ", " + sspObject);
                        break;
                    }
                    case ACTION_GEOFENCE_NOTIFICATION_SELECTED: {
                        final SspObject sspObject = getSspObjectFromIntent(intent);
                        Log.d(TAG, action + ": " + sspObject);
                        break;
                    }
                }
            }
        }
        @Nullable
        private SspObject getSspObjectFromIntent(@NonNull Intent intent) {
            SspObject sspObject = null;
            if(intent.hasExtra(KEY_SSP_ACT)) {
                try {
                    sspObject = SspAct.jsonToEntity(new JSONObject(intent.getStringExtra(KEY_SSP_ACT)));
                } catch (JSONException e) {
                    e.printStackTrace();
                }
            } else if(intent.hasExtra(KEY_SSP_CATEGORY)) {
                try {
                    sspObject = SspCategory.jsonToEntity(new JSONObject(intent.getStringExtra(KEY_SSP_CATEGORY)));
                } catch (JSONException e) {
                    e.printStackTrace();
                }
            } else if(intent.hasExtra(KEY_SSP_PRODUCT)) {
                try {
                    sspObject = SspProduct.jsonToEntity(new JSONObject(intent.getStringExtra(KEY_SSP_PRODUCT)));
                } catch (JSONException e) {
                    e.printStackTrace();
                }
            }
            return sspObject;
        }
    };
```

During detection, the user's location is monitored. Detection times vary according to OS settings. 

For IOS, the following applies: "When testing your region monitoring code in iOS Simulator or on a device, realize that region events may not happen immediately after a region boundary is crossed. To prevent spurious notifications, iOS doesn't deliver region notifications until certain threshold conditions are met. Specifically, the user's location must cross the region boundary, move away from the boundary by a minimum distance, and remain at that minimum distance for at least 20 seconds before the notifications are reported.

The specific threshold distances are determined by the hardware and the location technologies that are currently available. For example, if Wi-Fi is disabled, region monitoring is significantly less accurate. However, for testing purposes, you can assume that the minimum distance is approximately 200 meters."

### Handling detection and notification

```swift
// Handle incoming changes regarding Geofence status from the SDK.

extension RezolveGeofence: NearbyEngagementsManagerDelegate {
    func didStartMonitoring(for circularRegions: [CLCircularRegion], coordinate: CLLocationCoordinate2D, radius: Int) {
        print("didStartMonitoring")
    }
    
    func didEnter(circularRegion: GeofenceData) {
        print("didEnter")
    }
    
    func didFail(with error: Error) {
        print("didFail -> \(error.localizedDescription)")
    }
    
    func didUpdateCurrentDistanceFrom(location: CLLocationCoordinate2D, geofences: [GeofenceData], beacons: [BeaconData]) {
        print("didUpdateCurrentDistanceFrom")
    }
    
    func didReceiveInAppNotification(act: SspResolverAct?) {
        print("didReceiveInAppNotification")
    }
}
```
```java
// Taken from App.java in the Sample App project 

// The SDK sends the following broadcast messages (see code sample in "Detecting Geozones" above for details):

// When the notification is displayed in the notification center, the broadcast action is set to: 
ACTION_GEOFENCE_NOTIFICATION_DISPLAYED:

// And when the user taps the notification, the broadcast action is set to: 
ACTION_GEOFENCE_NOTIFICATION_SELECTED:

// Notes:
// The SDK shows a notification when it detects the geofence zone. 
// The SDK calls the server endpoint to get the object details, and if the object is active
//   it will show the notification and send the ACTION_GEOFENCE_NOTIFICATION_DISPLAYED broadcast.
// Use the geofenceBroadcastReceiver to fetch details about the trigger and SspObject.
// The fetched ssp object has to be in an active state for notification to be shown. 
// The SDK prevents showing duplicate notifications if the same geofence was detected 
//   during the silent period defined in the EngagementsUpdatePolicy.

```

When a geofence is detected, notification is potentially shown, as long as it is not within a silent period from a previous detection.
