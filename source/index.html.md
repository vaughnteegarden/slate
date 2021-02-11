---
title: Rezolve Inside (TM) SDK Documentation

language_tabs:
  - swift: IOS
  - java: Android/Java
  - kotlin: Android/Kotlin

toc_footers:
  - <a href='http://www.rezolve.com'>Rezolve Homepage</a>
  - <a href='https://github.com/tripit/slate'>Docs powered by Slate</a>

includes:
- howtobasic
- howtojwt
- howtoconsumerprofile
- howto-and-detectengagements
- howto-and-acttarget
- howto-and-prod-cat-url-target
- howto-ios-detectengagements
- howto-ios-acttarget
- howto-ios-prod-cat-target
- howto-ios-urltarget
- howtotriggermanager
- howtoinstantbuy
- howtocartbuy
- howtocategory
- howtomall
- howtosearch
- howtoclickandcollect
- howtobackgroundlistening
- howtolocationtrigger
- howtoact
- errorhandling
- moduleref
- changelog

search: true
---

# Introduction

```html
Note:

Code samples for IOS and Android will appear in this column.

You can switch between IOS and Android by clicking the tabs above.
```

The **Rezolve Inside<sup>TM</sup> SDK** is a software development kit that enables mobile app developers to integrate Rezolve’s mobile commerce and engagement capabilities into their new or existing mobile apps.

The Rezolve SDK communicates with the Rezolve online product stack – a set of dashboards and services designed to provide retailers and brands with everything they need to promote and sell via Rezolve, and also provide consumers with a seamless mobile experience.

## Reference App

A Rezolve Reference App is available. This app was designed to showcase the unique capabilities of the Rezolve system, and to demonstrate the consumer experience.

To get the app, go here:  <a href='https://help.rezolve.com/rezolve-reference-app/' target='_blank'>Rezolve Reference App</a>

Download links, sample media, and instructions are all included. 




## Capabilities

The Rezolve Inside<sup>TM</sup> SDK is a full-featured application suite. Capabilities that can be integrated into your application include:

* Enabling Shoppable Ads - <a href="images/RezolveOverview-ShoppableAds.pdf">Shoppable Ads PDF</a>
* Creating a browsable Mall - <a href="images/RezolveOverview-Mall.pdf">Mall PDF</a>
* Supporting Pre-pay account Top Up - <a href="images/RezolveOverview-TopUp.pdf">Top Up PDF</a>
* Consumer management of topup devices
* Scanning of Rezolve Encoded visual media
* Scanning of Rezolve Encoded audio media
* Consumer account creation, consumer profile, and purchase history
* Wallet management

<!-- ************************
The Rezolve Inside<sup>TM</sup> SDK and Platform feature a robust and state-of-the-art security model (see <a href="images/RezolveOverview-Security.pdf">see security overview PDF</a>).
***************************** -->


## Intended audience

This document is intended for experienced IOS and Android developer. It is expected that you have built apps before, and know your way around an IDE.

## Term Definitions

|term|definition|
|---|---|
|Partner|The Partner is the owner of a mobile application and audience. It is the Partner who wishes to integrate the Rezolve Inside<sup>TM</sup> SDK capabilities with their app. |
|Developer|The Developer is the mobile app developer of the Partner.|
|Merchant|A merchant runs an ecommerce site that offers products for sale. Merchants also create Shoppable Ads and link them to products. A merchant may also offer device accounts that need Top Up. |
|Consumer|The end user of the Partner's mobile app. The customer who buys merchant products.|
|SDK|Refers to the Rezolve Inside<sup>TM</sup> SDK, unless otherwise specified.|




## For more information

For more information on Rezolve, see <a href="http://rezolve.com">http://rezolve.com</a>.


# Getting Started

## Supported OS Versions

|OS|Minimum Version|
|---|---|---|
|IOS|IOS 9 and up<br>SWIFT 4 (recommended) or 3|
|Android|4.2.2 and up|


## Implement a Crash Reporting Sytem

To provide quality support, we require quality information. 

Rezolve advises using **<a href="https://fabric.io/kits/ios/crashlytics">Fabric Crashlytics</a>** to capture app problems on both IOS and Android.

We will not provide development support if you have not integrated Crashlytics or a similar tool.



## Download the Rezolve Inside<sup>TM</sup> SDK & Get an API Key

### <a href="license.html" target="_blank">Request API Key</a>

Latest release versions:

- Android: 3.1.0
- Compatible Android Scan version: 3.0.1

- IOS: 2.0.4.1

If this is your first time downloading the SDK, you will be provided with an API Key and the required environment information to begin development.


## Set up the SDK - IOS

The target IDE for IOS instructions is **XCode**. If you use a different IDE you may have to follow a different series of steps, please refer to your IDE documentation to understand how to incorporate third party SDKs into your IDE.

### CocoaPods

The Rezolve IOS SDK is distributed through <a href="https://cocoapods.org/about" target="_blank">CocoaPods</a>. In order to proceed with installing the SDK, as a prerequisite, first install the latest version of CocoaPods:

`sudo gem install cocoapods`

### Install Rezolve SDK

The SDK can now be installed in your own project by adding the following in `Podfile`:

```swift
platform :ios, '10.0'

use_modular_headers!

target 'Sample' do
  pod 'RezolveSDK'
end
```
### &nbsp;

Keep in mind that Sample is just a placeholder, and should be substituted by your own App Target name. Now just run the following command on a Terminal window pointing to the directory of your Podfile:

`pod install`

Don’t forget to use the `.xcworkspace` file to open your project in Xcode, instead of the `.xcodeproj` file, from here on out.



#### Updating the SDK

Every new release of the RezolveSDK can be updated by typing

`pod update ResolveSDK`

## Set up the SDK - Android

The target IDE for Android instructions is **Android Studio**. If you use a different IDE you may have to follow a different series of steps, please refer to your IDE documentation to understand how to incorporate third party SDKs into your IDE.

The Android SDK is distributed via the Rezolve Nexus/Maven repository. 

### Update root gradle.properties

```java
// in /gradle.properties
REZOLVE_SDK_REPOSITORY_URL=https://nexus.rezo.lv/repository/maven-sdk-releases/
REZOLVE_SDK_REPOSITORY_USERNAME=PUT_YOUR_USERNAME_HERE
REZOLVE_SDK_REPOSITORY_PASSWORD=PUT_YOUR_PASSWORD_HERE
```

Update the gradle.properties file in the root folder of your project and set variables for the repository url, username and repository password.  Your Nexus username and password will be assigned to you when you receive your API Key.

### Update root build.gradle to add repository

```java
// in /build.gradle
allprojects {
    repositories {
        mavenLocal()
        maven {
			url REZOLVE_SDK_REPOSITORY_URL
			credentials {
				username REZOLVE_SDK_REPOSITORY_USERNAME
				password REZOLVE_SDK_REPOSITORY_PASSWORD
			}
        }
        google()
        jcenter()
    }
}
```

Add the Rezolve repository to your root build.gradle file, in the `allprojects` block. 

### Update :app module build.gradle to add sdk dependency

```java
// in /app/build.gradle
dependencies {
	def rezolveSdkVersion = "3.0.0"
	def rezolveSdkScanVersion = "3.0.0"
    // ...
	implementation "com.rezolve.sdk:core-android:$rezolveSdkVersion"
    implementation "com.rezolve.sdk:payment-android:$rezolveSdkVersion"
    implementation "com.rezolve.sdk:ssp-android:$rezolveSdkVersion"
    implementation "com.rezolve.sdk:ssp-google-geofence:$rezolveSdkVersion"
    implementation "com.rezolve.sdk:resolver:$rezolveSdkVersion"
    implementation "com.rezolve.sdk:scan-android:$rezolveSdkScanVersion"
    // ...
}
```

Add Rezolve SDK dependency to build.gradle file in your :app module. Set the `rezolveSdkVersion` and `rezolveSdkScanVersion` to the version you wish to use. Note it is important to use double quotes as shown around the implementation strings, or `$rezolveSdkVersion` won't be evaluated. To upgrade to a new release, update `rezolveSdkVersion` and `rezolveSdkScanVersion` to the latest version.

### Permissions

```java
// Note: the SDK's manifest requests the following permissions:
// On Android 6.0+, you will have to specifically request the last two. 
// See https://developer.android.com/training/permissions/requesting.html

<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    
// RECORD_AUDIO permission is only needed if you implement the Audio Scan feature.
<uses-permission android:name="android.permission.RECORD_AUDIO" />
    
// CAMERA permission is only needed if you implement the Image Scan feature.
<uses-permission android:name="android.permission.CAMERA" />
    
// FOREGROUND_SERVICE permission is only needed if you implement the Background Listening feature.
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />

// Location permissions are only needed if you implement the Geofence detection feature.
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />


```