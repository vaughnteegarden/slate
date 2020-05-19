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
```kotlin

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
```

When a geofence is detected, the code to the right handles the geofence.
