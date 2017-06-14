---
title: Rezolve Inside (TM) SDK Documentation

language_tabs:
  - objective_c: IOS
  - java: Android

toc_footers:
  - <a href='http://www.rezolve.com'>Rezolve Homepage</a>
  - <a href='https://github.com/tripit/slate'>Documentation powered by Slate</a>

includes:
- howto
- moduleref

search: true
---

# Introduction

The **Rezolve Inside<sup>TM</sup> SDK** is a software development kit that enables mobile app developers to integrate Rezolve’s mobile commerce and engagement capabilities into their new or existing mobile apps.
## Capabilities

```html
Note:

Code samples for IOS and Android will appear in this column.

You can switch between IOS and Android by clicking the tabs above.
```

The Rezolve Inside<sup>TM</sup> SDK is a full-featured application suite. Capabilities that can be integrated into your application include:

* Enabling Shoppable Ads - <a href="images/RezolveOverview-ShoppableAds.pdf">see Shoppable Ads PDF</a>
* Creating a browsable Mall - <a href="images/RezolveOverview-Mall.pdf">see Mall PDF</a>
* Supporting Pre-pay account Top Up - <a href="images/RezolveOverview-TopUp.pdf">see Top Up PDF</a>
* Consumer account creation and Rezolve login
* Consumer profile and wallet management
* Consumer management of topup devices
* Scanning of Rezolve Encoded visual media
* Listening to Rezolve Encoded audio media
* Purchase history

## Intended audience

This document is intended for experienced IOS and Android developer. It is expected that you have built apps before, and know your way around an IDE.

## Term Definitions

|term|definition|
|---|---|
|Partner|The Partner is the owner of a mobile application and audience. It is the Partner who wishes to integrate the Rezolve Inside<sup>TM</sup> SDK cababilities with their app. |
|Developer|The Developer is the mobile app developer of the Partner.|
|Merchant|A merchant runs an ecommerce site that offers products for sale. Merchants also create Shoppable Ads and link them to products. A merchant may also offer device accounts that need Top Up. |
|Consumer|The end user of the Partner's mobile app. The customer who buys merchant products.|
|SDK|Refers to the Rezolve Inside<sup>TM</sup> SDK, unless otherwise specified.|


## For more information

For more information on Rezolve, see http://rezolve.com.


# Getting Started

## Get an API key

You will need an API key to use the Rezolve Inside<sup>TM</sup> SDK.  Please visit <a href="https://www.rezolve.com/#sectionForm">the contact page</a> and request an API key. We’ll establish some basic information to form a contract of use, and send for your approval. Upon signature, we will provide an API key for you to use.

## Download the SDK

The Rezolve Inside<sup>TM</sup> SDK for **IOS** can be downloaded here: <a href="https://www.rezolve.com/sdk-releases/rezolve-sdk-1.0-release.zip">rezolve-sdk-1.0-release.zip</a>

The Rezolve Inside<sup>TM</sup> SDK for **Android** can be downloaded here: <a href="https://www.rezolve.com/sdk-releases/rezolve-sdk-1.0-release.aar">rezolve-sdk-1.0-release.aar</a>

## Set up the SDK - IOS

The target IDE for IOS instructions is **XCode**. If you use a different IDE you may have to follow a different series of steps, please refer to your IDE documentation to understand how to incorporate third party SDKs into your IDE.

The IOS SDK is distributed as a .framework. This makes it easy to embed Rezolve capabilities in your app. The steps are as follows:

1. Open your existing project.<br/><br/>
2. Go to the app target’s General configuration page.<br/><br/>
3. Add the framework target to the Embedded Binaries section by clicking the Add icon, and picking the framework file you downloaded. Do not drag in the framework from Finder. <br/><img src="images/add-framework.png" style="margin:6px 0; border:1px solid #333;"><br/><br/>
4. Select the Rezolve framework from the list of binaries that can be embedded.


## Set up the SDK - Android

The target IDE for Android instructions is **Android Studio**. If you use a different IDE you may have to follow a different series of steps, please refer to your IDE documentation to understand how to incorporate third party SDKs into your IDE.

The Android SDK is distributed as an .aar library. This makes it easy to import Rezolve capabilities into your app. The steps are as follows:

<ol>
<li> In your project, change to the Project view, and add the .aar file you downloaded to the /libs folder. If prompted by Android Studio to select the file association, pick “Archive”. <br/><img src="images/import-1-addtolib.png" style="margin:6px 0;"><br/><img src="images/import-2-lib.png" style="margin:6px 0;"><br/><br/></li></ol>

```java
flatDir {
    dirs 'libs'
}
```

<ol start="2"><li>In the project-level gradle file, specify the libs dir as "flatDir". <br/><img src="images/import-3-addtogradle.png" style="margin:6px 0;"><br/><br/></li></ol>

```java
compile(name:'rezolve-sdk-1.0-release', ext: 'aar')
```

<ol start="3"><li>Lastly, in the application-level gradle file, add the Rezolve Inside<sup>TM</sup> SDK as a dependency (change filename/version as needed to match your download). If necessary, re-sync the project.<br/><img src="images/import-4-addtodepen.png" style="margin:6px 0;"><br/><br/></li><li>The SDK is ready to use.</li></ol>


