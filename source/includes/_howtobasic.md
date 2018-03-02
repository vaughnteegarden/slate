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

Your `server environment` is an enum. See values, right.



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
        .createSession(entityId, partnerId, deviceProfile, new RezolveInterface() {

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


##  User Management, Session Management

For the purpose of the SDK, it is assumed the partner has an existing community of consumers, and has a method of authenticating them in the partner app. It is further assumed the partner wishes to introduce their consumes to Rezolve capabilities. Each partner consumer that wishes to utilize Rezolve services will need a Rezolve account.

This section will describe how to:

1. Register a Rezolve user via the SDK
2. Create a Session for that user
3. When the user is done, log them out

<img src="images/loginsequencediagram.png" style="margin:6px 0;"><br/>[ <a href="images/loginsequencediagram.png">View full size</a> ]



### Register User

```swift
  let deviceProfile: DeviceProfile = DeviceProfile(
      deviceId: "123",
      make: "Apple",
      osType: "iOS",
      osVersion: "10",
      locale: "Europe/London"
  )

  let signUpRequest: SignUpRequest = SignUpRequest(
      email: "johndoe@domain.com",
      firstName: "John",
      lastName: "Doe",
      name: "John Doe",
      device: deviceProfile
  )


  sdk.registerUser(request: signUpRequest) { (partnerId: String, entityId: String) in
      self.entityId = entityId;
      self.partnerId = partnerId

      sdk.createSession(
          entityId: self.entityId!,
          partnerId: self.partnerId!,
          device: deviceProfile) { (session: RezolveSession) in

              self.mySession = session
      }
  }
```
```java
DeviceProfile deviceProfile = new DeviceProfile(deviceId, deviceManufacturer, locale);

SignUpRequest signUpRequest = new SignUpRequest.Builder()
    .email("john.doe@domain.com")
    .firstName("John")
    .lastName("Doe")
    .name("JDoe")
    .device(deviceProfile)
    .build();

RezolveSDK.getInstance(API_KEY, ENVIRONMENT).registerUser(signUpRequest, 
new RezolveInterface() {

    @Override
    public void onInitializationSuccess(RezolveSession rezolveSession, String partnerId, 
    String entityId) {

    	// persist the entity_id and partner_id values here
        ...

        // set session for convenience
        mySession = rezolveSession;
    }

    @Override
    public void onInitializationFailure() {
    	// handle failure here
    }
});

```

To register a user, call the sdk `registerUser` method, passing in an `SignUpRequest object`. The SignUpRequest object is defined as shown to the right. It is composed of a deviceProfile object, and four user data fields. All fields are strings.

The `registerUser` method is used as shown to the right. Upon successful registration, the response contains a `partner_id` and an `entity_id`. **Persist both these values for the life of the app.**

<aside class="notice">
You will know if you need to call `registerUser` instead of `createSession` if either `partner_id` or `entity_id` are null or blank.
</aside>

<aside class="warning">
It is recommended that <code>entity_id</code> and <code>partner_id</code> be persisted to the partner server. If these values are stored server side, a consumer who uninstalls the app and reinstalls will be able to retain their history and settings. Without this info, their account cannot be reassociated after install.
</aside>





### Create Session

```swift
let deviceProfile: DeviceProfile = DeviceProfile(
  deviceId: "123",
  make: "Apple",
  osType: "iOS",
  osVersion: "10",
  locale: "Europe/London"
)

sdk.createSession(
  entityId: self.entityId!,
  partnerId: self.partnerId!,
  device: deviceProfile) { (session: RezolveSession) in
      self.mySession = session
}

```

```java
RezolveSDK.getInstance(API_KEY, ENVIRONMENT).createSession(entityId, 
partnerId, deviceProfile, new RezolveInterface() {

    @Override
    public void onInitializationSuccess(RezolveSession rezolveSession, String s, 
    String s1) {
        mySession = rezolveSession;
        // use created session to access managers
    }

    @Override
    public void onInitializationFailure() {
        // handle failed initialization
    }
});


// Note: You don't need to create a new session if user navigates to another activity. 
// After the session is created, you can access it by calling:
RezolveSession rezolveSession = RezolveSDK.getInstance(API_KEY, ENVIRONMENT)
.getRezolveSession();

```



To log in and interact with Rezolve services, you must establish a session. A session combines several functions, and abstracts them from the developer:

* Verifies the validity of the mobile app, through the API key
* Identifies the consumer and pairs them with a session token
* Verifies each request from the mobile app by passing API Key and session token
* Establishes a public key for the session, for encrypting transmitted credit card info

Once the user is authenticated on the partner side, make a call to `createSession{}`, passing in the `entity_id` and a `deviceProfile` object. The server response contains a session authentication token, and a session public key; the SDK takes care of managing these for the session. The auth token is used to authenticate each request from the client, while the public key is used for encrypting the transmission of payment card information for this session.



Once the session is established, you can access services, for example:
`session.CustomerProfileManager.get`





### Logout Session

```swift
// When session ends you should inform the sdk by calling
session.authenticationManager.logout();
```
```java
// When session ends you should inform the sdk by calling
rezolveSession.getAuthenticationManager().logout(entityId);
```

When a session is over, you should notify the SDK by passing the `entity_id` to the logout method.



## Consumer Profile Management


Once logged in, you have access to the consumer's records. These include:

* Consumer Profile - Via the `ConsumerProfileManager`. Name, email, and device profile (phone info) for the consumer
* Address Book - Via the `AddressbookManager`. A collection of postal addresses, to be used for ship-to and bill-to purposes
* Favorites - Via the `FavouriteManager`. A collection of devices that can be topped up.  A favorite can represent a mobile phone, a tollway transponder, or other device/account.
* Wallet - Via the `WalletManager`. Wallet lets you store credit card info securely, and lets the consumer maintain the list of cards. There can be multiple cards.

There are no specific flows to consider when managing the customer profile and associated records.

AddressbookManager, FavouriteManager, and WalletManager support the following CRUD operations: `create`, `update`, `delete`, `getAll`, `get`.

ProfileManager supports only `update` and `get`.




