# SDK Feature Use

This section describes the usage of the SDK to build specific feature-related functionalities.



## Basic Usage

```swift
import UIKit

// Import the following library:
import RezolveSDK

class RezolveSDK1ViewController: ViewController {
    override func viewDidLoad() {
        super.viewDidLoad()

        // initialize SDK
        // possible values for RezolveEnv enum are .Sandbox, .Production
        let API_KEY: String  = "1234567890"
        let ENVIRONMENT: String = "https://sandbox-api-tw.rzlvtest.co"
        let sdk: RezolveSDK = RezolveSDK(apiKey: API_KEY, env: ENVIRONMENT)
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
    }
}

```

```java
// Import the following classes:
import com.rezolve.sdk.RezolveInterface;
import com.rezolve.sdk.RezolveSDK;
import com.rezolve.sdk.RezolveSession;
import com.rezolve.sdk.model.customer.DeviceProfile;
import com.rezolve.sdk.model.foreign.SignUpRequest;

// To handle server responses you will need to import interfaces or
// abstract classes from the following packages:
import com.rezolve.sdk.core.callbacks.*;
import com.rezolve.sdk.core.interfaces.*;


// Rezolve SDK operates on models from com.rezolve.sdk.model subpackages. 
// For example to handle a response from getMerchants query you will need 
// a Merchant model:

import com.rezolve.sdk.model.shop.Merchant;



// initialize SDK

private final static String API_KEY = "your_api_key";
private final static String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co";

RezolveSDK sdk = RezolveSDK.getInstance(API_KEY, ENVIRONMENT);

```

```java
// Note: the SDK's manifest requests the following permissions:
// On Android 6.0+, you will have to specifically request the last two. 
// See https://developer.android.com/training/permissions/requesting.html

<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.CAMERA" />

```

To get started, import the SDK into your code file.

The SDK must be initialized before use. When initializing the SDK, you must specify your `API Key`, and the `server environment` you are targeting.

Your `API Key` is supplied to you when you set up a developer account with Rezolve.

Your `server environment` is a string, and is provided at the same time as your API key.



## Android-specific instructions on Managers

```java

// 	THE FOLLOWING TWO METHODS ARE EQUIVALENT, but Interface saves effort

//
// Using MANAGER, you must handle the response in a WalletCallback:
//

public class MyActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
 		...
        RezolveSDK.getInstance(API_KEY, ENVIRONMENT)
        .createSession(accessToken, entityId, partnerId, deviceProfile, new RezolveInterface() {

            @Override
            public void onInitializationSuccess(RezolveSession rezolveSession, String 
            entityId, String partnerId) {
                rezolveSession.getWalletManager().getAll(new WalletCallback() {

                    @Override
                    public void onWalletGetAllSuccess(List<PaymentCard> list) {

                    // handle getAll response here
                        for(PaymentCard paymentCard : list) {
                            String cardId = paymentCard.getId();
                            String expiresOn = paymentCard.getExpiresOn();
                            // ...etc
                    	}
                    }
                });
            }
        });
    }
}

//
// Using INTERFACE, you can save some development time
//

public class MyActivity extends AppCompatActivity implements WalletInterface {
	...
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		// .getRezolveSession gets an already created session
		RezolveSDK.getInstance(API_KEY, ENVIRONMENT).getRezolveSession()
        .getWalletManager().getAll(this);
	}

	@Override
	public void onWalletGetAllSuccess(List<PaymentCard> list) {
		// handle getAll response here
		for(PaymentCard paymentCard : list) {
    		String cardId = paymentCard.getId();
			String expiresOn = paymentCard.getExpiresOn();
            // ...etc
		}
	}
}
```

In the Android version of the Rezolve Inside<sup>TM</sup> SDK, each FeatureManager has an equivalent FeatureInterface.

For example, to use `WalletManager` methods, you can either do so directly, or implement `WalletInterface` in your current activity for convenience. See Android code at right for comparison.

The Module Reference section exclusively documents using Feature Interfaces, as this is the preferred method.

## Automatic thumbnail generation

> Example JSON for 1 large image, and 4 associated "thumbnail" images



```JSON
      "image": "https:\/\/s3.amazonaws.com\/1\/27\/Image.png",
      "image_thumbs": [
        "https:\/\/s3.amazonaws.com\/1\/27\/Image_thumb_400x550.png",
        "https:\/\/s3.amazonaws.com\/1\/27\/Image_thumb_800x1100.png",
        "https:\/\/s3.amazonaws.com\/1\/27\/Image_thumb_1200x1651.png",
        "https:\/\/s3.amazonaws.com\/1\/27\/Image_thumb_1600x2201.png"
      ],
```

When large images are uploaded to the merchant portal, the portal automatically generates a series of smaller images, which are referred to as thumbnails even though some are fairly large. The rules for thumbnail generation are as follows:


* Generated images respect the aspect ratio of the original image; their height/width ratio will not be distorted
* If the uploaded image is: 
	* smaller or equal to 400px maximum dimension (width or height), no resized image is produced.
	* larger than 400px maximum dimension, it makes a 400px max dimension image. (referred to as the "thumbnail" image)
    * larger than 800px maximum dimension, it makes a 800px max dimension image, and all smaller sizes.
    * larger than 1200px maximum dimension, it makes a 1200px max dimension image, and all smaller sizes.
	* larger than 1600px maximum dimension, it makes a 1600px max dimension image, and all smaller sizes.
* Additionally, the original upload image always returned, even if gigantic. Consider if you need images larger than 1600x1600 in your app.
* This applies to all merchant uploaded images that are supplied to the SDK. 

Each uploaded image will have a corresponding array of thumbnails. If an object can have an array of images, it will have an array of thumbnail arrays.


##  User Management Introduction

For the purpose of the SDK, it is assumed the partner has an existing community of consumers, and has a method of authenticating them in the partner app. It is further assumed the partner wishes to introduce their consumers to Rezolve capabilities. Each partner consumer that wishes to utilize Rezolve services will need a Rezolve account.

The Rezolve SDK has no built-in authentication mechansim, but that is not to say there is no security. Instead, Rezolve is utilizing a server-to-server JWT authorization system, conformant with the **https://tools.ietf.org/html/rfc7519** standard. If you are not familar with JSON Web Tokens, the site **https://jwt.io/** provides an excellent primer on the use of JWTs, as well as links to various JWT libraries you can utilize.

Exactly how you implement authentication in your app will depend on what authorization system you use. You may either:

- Utilize an existing authentication server, and adapt it to serve the JWT required by Rezolve. This makes most sense if you already have a community of users with logins.

- Utilize Rezolve User Authentication (RUA), our implementation of a JWT-aware auth system. If you do not have an existing auth system, adopting RUA may save development time, as opposed to implementing a new system from the ground up.

The primary task in implementing user management is to create the JWT that will allow you to bind a Partner user to a Rezolve profile. Through a server-to-server transaction, you can create a new Rezolve User Profile, and get back an identifying `entityId` that you should persist in your authentication database, as an addition to your user record. This `entityId` will then be used by the Rezolve SDK to identify the user to the Rezolve Server. 

## Session Management Introduction

Once a Rezolve Profile has been established, and an `entityId` secured, there is no Login per-se in the Rezolve system. Whether or not the SDK can talk to Rezolve Services depends on supplying a valid JWT to the SDK from your auth system. If the user-specific JWT you supply is valid, you are able to create a session, and access the SDK capabilities. 

Expiring a session is as simple as expiring the JWT token. So it is up to the partner's auth server to either renew the JWT when it expires, or to set an expiration when the partner consumer logs out. 

More on User Management and Session Management can be sound in the sections on JWT Authorization and RUA.  Look at whichever section is relevant to your situation.




## Consumer Profile Management


Once you have established a session, you have access to the consumer's records. These include:

* Consumer Profile - Via the `ConsumerProfileManager`. Name, email, and device profile (phone info) for the consumer
* Address Book - Via the `AddressbookManager`. A collection of postal addresses, to be used for ship-to and bill-to purposes.
* Phone Book - Via the `PhonebookManager`. A collection of phone numbers associated with the profile. 
* Favorites - Via the `FavouriteManager`. Reserved for future functionality, "Favorites" are collections of unique devices that can be topped up.  A favorite can represent a mobile phone, a tollway transponder, or other device/account.
* Wallet - Via the `WalletManager`. Wallet lets you store credit card info securely, and lets the consumer maintain the list of cards. There can be multiple cards.

There are no specific flows to consider when managing the customer profile and associated records.

AddressbookManager, FavouriteManager, PhonebookManager and WalletManager support the following CRUD operations: `create`, `update`, `delete`, `getAll`, `get`.

ProfileManager supports only `update` and `get`.




