# SDK Feature Use

This section describes the usage of the SDK to build specific feature-related functionalities.



## Basic Usage

```swift
import UIKit
import RezolveSDK


class SandboxViewController: UIViewController {

    private var API_KEY: String = "your_api_key"
    private var API_ENVIRONMENT: String = "sandbox-api-tw.rzlvtest.co"
    private var accessToken: String = "abc123.abc123.abc123"
    private var entityId: String = "123"
    private var partnerId: String = "123"
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        var sdk = RezolveSDK(
            apiKey: API_KEY,
            env: API_ENVIRONMENT
        )
        
        sdk.createSession(accessToken: accessToken, entityId: entityId, partnerId: partnerId) { session
            
            // your rezolve SDK logic here
        }
    }
}

```
```java
String API_KEY = "your_api_key";
String ENVIRONMENT = "https://sandbox-api-tw.rzlvtest.co/api";
String accessToken = "abc123.abc123.abc123";  // JWT token from auth server
String entityId = "123";	// from auth server
String partnerId = "123";   // from auth server
String deviceId = "wlkCDA2Hy/CfMqVAShslBAR/0sAiuRIUm5jOg0a"; // from stored device_id, see "Generating the device_id" above

// Use builder to create instance of SDK and set SDK Params
// Pass in an AuthRequestProvider here, to handle expiring JWT tokens
rezolveSDK = new RezolveSDK.Builder()
               .setApiKey(API_KEY)
               .setEnv(ENVIRONMENT)
               .setAuthRequestProvider(new PartnerAuthRequestProvider(AuthService.getInstance()))
               .build();

// Set JWT Auth Token from partner auth server
rezolveSDK.setAuthToken(accessToken);

// Start session, again supplying JWT auth token
rezolveSDK.createSession( accessToken, entityId, partnerId, new RezolveInterface() {

	@Override
	public void onInitializationSuccess(RezolveSession rezolveSession, String entityId, String partnerId) {
        // set device_id so it can be passed in x-header
        RezolveSDK.setDeviceIdHeader(deviceId);
    
		// use created session to access managers.  Example...
		rezolveSession.getAddressbookManager().get(...);
	}

	@Override
	public void onInitializationFailure() {
		// handle error
	}
});
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

Initializing the SDK requires: 

- API-Key - assigned when you signed up for API access
- Environment url - usually `https://sandbox-api-tw.rzlvtest.co` for first time implementations
- an Access Token - a JSON Web Token created by your auth server
- an Entity Id - this is the unique id of the user 
- a Partner Id - this is your partner id, assigned when you signed up for API access
- a Device Id - a unique identifier for the smartphone. 
 
More info on all these parameters may be found in **<a href="#jwt-authentication">JWT Authentication</a>**.
 


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
        rezolveSDK = new RezolveSDK.Builder()
           .setApiKey(API_KEY)
           .setEnv(ENVIRONMENT)
           .setAuthRequestProvider(new PartnerAuthRequestProvider(AuthService.getInstance()))
           .build();

            rezolveSDK.setAuthToken(accessToken);

            rezolveSDK.createSession( accessToken, entityId, partnerId, new RezolveInterface() {


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
		RezolveSDK.getInstance().getRezolveSession()
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

The Rezolve SDK has no built-in authentication mechansim, but that is not to say there is no security. Rezolve is utilizing a server-to-server JWT authorization system, conformant with the **https://tools.ietf.org/html/rfc7519** standard. If you are not familar with JSON Web Tokens, the site **https://jwt.io/** provides an excellent primer on the use of JWTs, as well as links to various JWT libraries you can utilize.

Exactly how you implement authentication in your app will depend on what authorization system you use. You may either:

- Utilize an existing authentication server, and adapt it to serve the JWT required by Rezolve. This makes most sense if you already have a community of users with logins.

- Utilize Rezolve User Authentication (RUA), our implementation of a JWT-aware auth system. If you do not have an existing auth system, adopting RUA may save development time, as opposed to implementing a new system from the ground up. Your Rezolve sales representative can help you with this decision.

The primary task in implementing user management is to create the JWT that will allow you to bind a Partner user to a Rezolve profile. Through a server-to-server transaction, you can create a new Rezolve User Profile, and get back an identifying `entityId` that you should persist in your authentication database, as an addition to your user record. This `entityId` will then be used by the Rezolve SDK to identify the user to the Rezolve Server. 

A valid JWT is required to initialize the SDK and start a sesssion. 







